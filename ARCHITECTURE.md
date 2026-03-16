# Architecture Overview — Syracuse City Guide

## System Context

The Syracuse City Guide is a civic tourism web application built on top of the
City of Syracuse Open Data Portal, supplemented by external APIs for data the
city does not publish (restaurants, events, emergency services).

The frontend is a single-file HTML/CSS/JS application deployable as a static
site with zero build steps. The backend is a FastAPI REST API backed by
PostgreSQL + PostGIS and pgvector, designed to support a future mobile app
and multi-city expansion.

---

## Four-Tier Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  CLIENT                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────┐ │
│  │ Static HTML  │  │ Voice Chat   │  │ Offline SQLite     │ │
│  │ index.html   │  │ STT / TTS    │  │ (emergency bundle) │ │
│  └──────────────┘  └──────────────┘  └────────────────────┘ │
└───────────────────────────┬─────────────────────────────────┘
                            │ HTTPS / REST / SSE
┌───────────────────────────▼─────────────────────────────────┐
│  EDGE                                                        │
│  FastAPI API Gateway  ·  Redis Cache  ·  JWT Auth            │
└───────────────────────────┬─────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────┐
│  SERVICES                                                    │
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────┐ │
│  │ Places Svc   │  │ RAG / Chat   │  │ Event Aggregator   │ │
│  │ PostGIS      │  │ OpenAI       │  │ Eventbrite + TM    │ │
│  └──────────────┘  └──────────────┘  └────────────────────┘ │
└───────────────────────────┬─────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────┐
│  DATA                                                        │
│  PostgreSQL + PostGIS   pgvector   Redis   S3                │
└─────────────────────────────────────────────────────────────┘
```

---

## Key Design Decisions

### 1. Single-file frontend
The entire tourist guide is one self-contained HTML file. This means:
- Zero build tooling required
- Deployable to GitHub Pages, S3, or any CDN in seconds
- Works offline after first load (data embedded in JS)
- No framework dependency to maintain

### 2. PostGIS for geospatial queries
All place records store their location as a PostGIS `GEOMETRY(Point, 4326)` column
with a GIST index. Nearby searches use `ST_DWithin` (correct distance on a sphere)
combined with the KNN operator `<->` for ordering by distance.

Example query pattern:
```sql
SELECT *, ST_Distance(geom::geography, ref::geography) AS distance_m
FROM places
WHERE ST_DWithin(geom::geography, ST_MakePoint(:lng,:lat)::geography, :radius_m)
  AND category = :category
ORDER BY geom <-> ST_MakePoint(:lng,:lat)::geography
LIMIT :limit;
```

### 3. Hybrid RAG retrieval
The chatbot uses three retrieval signals combined before reaching the LLM:

| Signal          | Implementation             | Weight  |
|-----------------|----------------------------|---------|
| Vector similarity | pgvector cosine (`<=>`)  | Primary |
| Keyword match   | PostgreSQL `tsvector`      | Boost   |
| Geo-proximity   | PostGIS `ST_DWithin`       | Filter  |

The geo-filter is applied **before** the vector search as a hard constraint —
a query for "coffee near the airport" will never return results miles away
regardless of how semantically similar they are.

### 4. Fallback-first chatbot
The LLM is instructed never to answer outside its retrieved context.
If `_retrieve()` returns an empty set, the service returns the hardcoded string:

> "Sorry, this information is currently unavailable."

This prevents hallucination about Syracuse landmarks, phone numbers, or hours.

### 5. Emergency data is always offline
Emergency service records (hospitals, police, fire) are:
- Embedded as static data in `emergency_service.py` (no DB needed)
- Included in the HTML frontend as hardcoded JS constants
- Always available with zero network connectivity

### 6. Multi-city by design
The schema includes a `cities` table as the root entity. Every place, event,
and itinerary is keyed to a `city_id`. Adding a new city requires:
1. Insert a row into `cities`
2. Write a `data_source_manifest.yaml` for that city
3. Run `python etl/pipeline.py --run all --city=rochester`
4. Flip the feature flag in the API config

No code changes are required.

---

## Data Flow — ETL Pipeline

```
Syracuse ODP  ──┐
Yelp API      ──┤                                      ┌─ PostGIS (places)
HIFLD         ──┼─► Celery Worker ─► Normalizer ─────►─┤
Eventbrite    ──┤       │              Validator        ├─ pgvector (embeddings)
OSM Overpass  ──┘       │              Geo-enricher     └─ Redis (hot cache)
                        │
                        └─► Embedder (OpenAI) ─► pgvector upsert
```

Each ingestion job is idempotent — re-running it will upsert, not duplicate.
Records are keyed on `(source, source_id)` with a UNIQUE constraint.

---

## Data Source License Compliance

| Source          | License          | Constraint enforced                    |
|-----------------|------------------|----------------------------------------|
| Syracuse ODP    | Open data / CC0  | Attribution in UI footer               |
| OpenStreetMap   | ODbL             | Attribution required, share-alike      |
| Yelp Fusion     | Yelp ToS         | `cached_until` TTL = 24 hours          |
| Eventbrite      | Eventbrite ToS   | Deep-link only, no ticket inventory    |
| HIFLD / DHS     | Public domain    | No restrictions                        |

---

## Scalability Path

| Scale trigger          | Action                                           |
|------------------------|--------------------------------------------------|
| > 500k place records   | Rebuild IVFFlat index with `lists = 1000`        |
| > 5 cities             | Partition `places` table by `city_id`            |
| > 1k chat req/min      | Add read replicas; cache common queries in Redis |
| > 10 cities            | Move to managed PostGIS (AWS RDS / Supabase)     |
| Mobile app needed      | Backend API is already mobile-ready (REST/SSE)   |
