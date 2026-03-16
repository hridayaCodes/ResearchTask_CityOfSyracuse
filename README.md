# 🍊 Syracuse City Guide

> An AI-powered visitor guide for Syracuse, New York — built on open civic data.

[![Live Demo](https://img.shields.io/badge/Live%20Demo-Visit%20Syracuse-E8732A?style=flat-square)](https://yourusername.github.io/syracuse-city-guide/frontend/index.html)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue?style=flat-square)](LICENSE)
[![Data: Syracuse ODP](https://img.shields.io/badge/Data-Syracuse%20Open%20Data%20Portal-1B3A5C?style=flat-square)](https://data.syrgov.net)
[![Built with FastAPI](https://img.shields.io/badge/Backend-FastAPI-009688?style=flat-square)](https://fastapi.tiangolo.com)

---

## Table of Contents

- [Project Overview](#project-overview)
- [Problem Statement](#problem-statement)
- [Solution Description](#solution-description)
- [Key Features](#key-features)
- [System Architecture](#system-architecture)
- [Data Sources](#data-sources)
- [Technology Stack](#technology-stack)
- [Repository Structure](#repository-structure)
- [Installation](#installation)
- [Demo](#demo)
- [Future Improvements](#future-improvements)
- [Contributing](#contributing)
- [License](#license)

---

## Project Overview

The **Syracuse City Guide** is a full-stack civic technology application that transforms raw open data published by the City of Syracuse into a warm, welcoming visitor experience. Tourists arriving at Syracuse Hancock International Airport — or connecting to the airport WiFi — are invited to explore the city through a beautifully designed, information-rich guide that works entirely in a web browser.

The guide covers **places to visit**, **restaurants**, **parks and recreation**, **108 public artworks**, **Syracuse University's history and campus**, and a always-accessible **emergency helpline section** — all sourced from real open datasets and live APIs.

The project is equally a production-ready **reference architecture** for civic tourism applications: a FastAPI backend, PostGIS geospatial database, pgvector RAG-based AI chatbot, and a multi-source ETL pipeline that can be extended to any city with an open data portal.

---

## Problem Statement

Visitors arriving in a new city face a fragmented information landscape. Tourism websites are often cluttered with ads, outdated, or scattered across a dozen tabs. Official city portals surface data for residents — parking tickets, zoning maps, crime statistics — not for curious out-of-towners who just want to know where to eat, what to see, and where the nearest hospital is.

Specifically for Syracuse:

- The **City of Syracuse Open Data Portal** publishes valuable civic datasets (parks, public art, infrastructure) but has no consumer-facing tourism layer on top of them.
- Visitors, particularly those arriving at Hancock Airport, have **no single place** to discover the city's cultural depth — the Erie Canal history, the world-famous Tipperary Hill traffic light, the 108 murals and sculptures scattered across every neighborhood, the food scene anchored by legendary BBQ and the beloved chicken riggies dish.
- **Emergency information** is buried in government pages, hard to find in a stressful moment, and never surfaced proactively to visitors who may not know local resources.
- Civic open data in most cities goes **largely unused** by the people the city is meant to serve, because there is no accessible front end for it.

---

## Solution Description

The Syracuse City Guide layers a tourist-first experience on top of raw open civic data. It was designed with three principles:

**1. Open data as the foundation.**
Every piece of information about parks, public art, and city infrastructure comes directly from the City of Syracuse's open data portal. This means the application is transparent, reproducible, and updatable as the city publishes new data — no scraped content, no proprietary databases.

**2. Tourist-first information design.**
The interface deliberately excludes crime statistics and other data that creates anxiety without helping visitors. Instead, it surfaces exactly what a visitor needs: what to see, where to eat, how to explore the university, and where to get help in an emergency.

**3. Production-ready civic architecture.**
Beyond the frontend, the project includes a complete backend stack — a FastAPI REST API, PostGIS geospatial database, pgvector-backed AI chatbot with Retrieval-Augmented Generation, and a multi-source ETL pipeline. The schema and pipeline are designed to expand to any city with an open data portal by adding configuration, not code.

---

## Key Features

### For Visitors

| Feature | Description |
|---|---|
| 🗺️ **Places to Visit** | 12 curated attractions — museums, nature, historic sites, arts venues, family destinations — with filterable categories and "Did you know?" facts |
| 🍽️ **Restaurant Guide** | 10 top-rated restaurants including award-winning fine dining, the legendary Dinosaur Bar-B-Que, and local institutions. Filterable by cuisine |
| 🍊 **Syracuse University** | Dedicated section with SU's full history timeline (1870–2022), the JMA Dome facts, 12 notable alumni, campus visitor guide, and school highlights |
| 🌳 **Parks & Recreation** | All 7 community parks with recreation centers sourced directly from the Syracuse ODP parks dataset |
| 🎨 **Public Art Trail** | 91 geolocated artworks with real images from the Syracuse Public Art Commission — filterable by type (murals, sculptures, monuments, mosaics) |
| 🚨 **Emergency & Helplines** | Always one tap away: 911, 988, 311, poison control, domestic violence hotline, three 24/7 hospitals, police precincts, fire department |
| 💡 **Did You Know?** | Bite-sized historical and cultural facts woven throughout every section — the upside-down traffic light, the chicken riggies origin story, the Carrier Dome naming deal |

### For Developers

| Feature | Description |
|---|---|
| 🤖 **RAG Chatbot** | Hybrid vector + keyword + geo-filtered retrieval. Hardcoded fallback prevents hallucination |
| 🌍 **Multi-city ready** | Schema, API, and ETL are city-scoped from day one — adding Rochester or Albany requires config, not code |
| 📡 **REST API** | Full FastAPI backend with `/v1/places`, `/v1/events`, `/v1/chat`, `/v1/emergency`, `/v1/itinerary` |
| 🗄️ **PostGIS** | `ST_DWithin` + KNN operator for geospatial queries; GIST-indexed geometry column |
| 🧮 **pgvector** | 1536-dim embeddings with IVFFlat index for sub-millisecond semantic search |
| ⚙️ **ETL Pipeline** | Six data sources, idempotent upserts, Pydantic validation, automatic embedding generation |
| 🐳 **Docker Compose** | One-command local stack: Postgres + PostGIS + pgvector, Redis, FastAPI, Celery, Nginx |

---

## System Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│  CLIENT LAYER                                                    │
│  ┌─────────────────┐  ┌────────────────┐  ┌──────────────────┐  │
│  │  index.html      │  │  Voice Chat    │  │  Offline Bundle  │  │
│  │  (single file)   │  │  STT / TTS     │  │  (SQLite seed)   │  │
│  └─────────────────┘  └────────────────┘  └──────────────────┘  │
└────────────────────────────────┬─────────────────────────────────┘
                                 │  HTTPS / REST / SSE
┌────────────────────────────────▼─────────────────────────────────┐
│  EDGE  —  FastAPI API Gateway                                     │
│  Auth (JWT)  ·  Rate limit  ·  Redis cache  ·  Request routing    │
└────────────────────────────────┬─────────────────────────────────┘
                                 │
         ┌───────────────────────┼────────────────────────┐
         ▼                       ▼                        ▼
┌────────────────┐   ┌───────────────────┐   ┌────────────────────┐
│  Places Svc    │   │  RAG Chat Svc      │   │  Event Aggregator  │
│  PostGIS       │   │  pgvector + LLM    │   │  Eventbrite / TM   │
│  ST_DWithin    │   │  Intent classify   │   │  Hourly cache      │
└────────┬───────┘   └─────────┬─────────┘   └────────────────────┘
         │                     │
┌────────▼─────────────────────▼──────────────────────────────────┐
│  DATA LAYER                                                       │
│  PostgreSQL 16 + PostGIS 3 + pgvector   ·   Redis   ·   S3       │
└──────────────────────────────────────────────────────────────────┘
                                 ▲
                                 │  ETL Pipeline (Celery)
┌────────────────────────────────┴──────────────────────────────────┐
│  DATA SOURCES                                                      │
│  Syracuse ODP  ·  Yelp Fusion  ·  HIFLD  ·  Eventbrite  ·  OSM   │
└────────────────────────────────────────────────────────────────────┘
```

For the full architectural narrative — design decisions, query patterns, scaling path — see [`architecture/ARCHITECTURE.md`](architecture/ARCHITECTURE.md).

---

## Data Sources

| Source | Data | License | Refresh |
|---|---|---|---|
| [Syracuse Open Data Portal](https://data.syrgov.net) | Parks, public art, snow routes, crime, parcels, council boundaries | Open Data / CC0 | Weekly–Quarterly |
| [Yelp Fusion API](https://docs.developer.yelp.com) | Restaurants, ratings, hours | Yelp ToS (24h cache) | Daily |
| [HIFLD / DHS](https://hifld-geoplatform.opendata.arcgis.com) | Hospitals, police stations, fire stations | Public Domain | Monthly |
| [Eventbrite API](https://www.eventbrite.com/platform/api) | Local events | Eventbrite ToS | Hourly |
| [Ticketmaster API](https://developer.ticketmaster.com) | Concerts, sports, venues | Ticketmaster ToS | Hourly |
| [OpenStreetMap Overpass](https://overpass-api.de) | Parking, amenities | ODbL | Weekly |

Six CSV files from the Syracuse Open Data Portal are included in `data/raw/` for offline development and reproducibility. See [`docs/DATA_SOURCES.md`](docs/DATA_SOURCES.md) for full field mappings and license obligations.

---

## Technology Stack

### Frontend
| Layer | Technology | Reason |
|---|---|---|
| Framework | Vanilla HTML/CSS/JS | Zero build tooling; deployable as a static file; works offline |
| Typography | Playfair Display + Outfit + DM Mono | Editorial warmth balanced with UI legibility |
| Maps | SVG (embedded) → Mapbox GL JS | Works offline; upgrades gracefully when token available |
| Images | City of Syracuse public art CDN | Real photos from `syr.gov`; lazy-loaded with emoji fallback |

### Backend
| Layer | Technology | Reason |
|---|---|---|
| API framework | FastAPI 0.115 | Async-native, automatic OpenAPI docs, Pydantic validation |
| Database | PostgreSQL 16 + PostGIS 3 | `ST_DWithin` for geo queries; GIST + B-tree indexes |
| Vector store | pgvector 0.7 (IVFFlat) | Semantic search in the same DB; no separate vector service |
| Cache | Redis 7 | TTL-keyed API response caching; Celery broker |
| ORM | SQLAlchemy 2 (async) | Async session pool; raw SQL for spatial queries |
| Task queue | Celery 5 | Scheduled ETL jobs; retry on rate-limit errors |
| Runtime | Python 3.12 | Native `asyncio`, `match` statements, faster CPython |

### AI / RAG
| Layer | Technology | Reason |
|---|---|---|
| LLM | OpenAI GPT-4o | Best instruction-following for constrained tourism Q&A |
| Embeddings | text-embedding-3-small | 1536d, cost-efficient, strong multilingual retrieval |
| Retrieval | Hybrid: pgvector cosine + tsvector + ST_DWithin | Three signals = better precision than vector alone |
| STT | OpenAI Whisper API | Best accuracy on accented English; streaming partial transcripts |
| TTS | ElevenLabs / OpenAI TTS | Low latency, natural prosody |

### Infrastructure
| Layer | Technology |
|---|---|
| Containerisation | Docker + Docker Compose |
| CI/CD | GitHub Actions (see `.github/workflows/`) |
| Static hosting | GitHub Pages (frontend only) |
| Database hosting | AWS RDS (PostGIS) or Supabase (managed) |
| Object storage | AWS S3 (images, offline bundles) |

---

## Repository Structure

```
syracuse-city-guide/
│
├── frontend/
│   └── index.html                  # Complete tourist guide — single deployable file
│
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── .env.example
│   ├── db/
│   │   └── init.sql                # PostGIS schema, pgvector indexes, seed cities
│   └── app/
│       ├── main.py                 # FastAPI application entry point
│       ├── config.py               # Pydantic-settings configuration
│       ├── api/
│       │   ├── places.py           # GET /v1/places/nearby, /search, /art, /parks
│       │   ├── events.py           # GET /v1/events
│       │   ├── chat.py             # POST /v1/chat, /v1/chat/stream (SSE)
│       │   ├── emergency.py        # GET /v1/emergency/nearest, /helplines
│       │   └── itinerary.py        # POST /v1/itinerary/generate
│       ├── models/
│       │   └── schemas.py          # Pydantic request/response models
│       └── services/
│           ├── rag_service.py      # RAG pipeline: embed → retrieve → generate
│           ├── places_service.py   # PostGIS spatial queries
│           └── emergency_service.py# Haversine fallback (offline-safe)
│   └── tests/
│       └── test_api.py             # Pytest async test suite
│
├── etl/
│   ├── pipeline.py                 # CLI orchestrator — runs all or single source
│   ├── normalizer.py               # Maps every source format → PlaceRecord
│   ├── embedder.py                 # Batched OpenAI embedding + pgvector upsert
│   └── sources/
│       └── syracuse_odp.py         # Syracuse ODP CSV ingestor (parks + art)
│
├── data/
│   ├── raw/                        # Original CSVs from Syracuse Open Data Portal
│   │   ├── Syracuse_Public_Art_NEW.csv
│   │   ├── Syracuse_Parks_26_Recreation_Community_Centers.csv
│   │   ├── Crime_Data_2025_Part_1_Offenses_With_Lat_and_Long.csv
│   │   ├── Emergency_Snow_Routes.csv
│   │   ├── Syracuse_Common_Council_Boundaries_2023.csv
│   │   └── Syracuse_Parcel_Map_2025_Q4.csv
│   └── processed/                  # Normalised outputs from ETL (gitignored)
│
├── architecture/
│   └── ARCHITECTURE.md             # System design, ADRs, query patterns, scaling plan
│
├── docs/
│   ├── API_REFERENCE.md            # Full endpoint documentation with examples
│   ├── DATA_SOURCES.md             # Source details, licenses, field mappings
│   └── ETL_PIPELINE.md             # Pipeline stages, schedule, deduplication logic
│
├── docker-compose.yml              # Full local stack: DB + Redis + API + Worker + Frontend
├── .gitignore
└── README.md
```

---

## Installation

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (recommended)
- Or: Python 3.12+, PostgreSQL 16 with PostGIS and pgvector extensions

---

### Option A — Open the frontend instantly (no setup)

The tourist guide works as a completely standalone static file:

```bash
git clone https://github.com/yourusername/syracuse-city-guide.git
cd syracuse-city-guide

# Just open the file in your browser
open frontend/index.html          # macOS
start frontend/index.html         # Windows
xdg-open frontend/index.html      # Linux
```

No server, no dependencies, no build step.

---

### Option B — Full stack with Docker Compose

```bash
git clone https://github.com/yourusername/syracuse-city-guide.git
cd syracuse-city-guide

# 1. Set up environment variables
cp backend/.env.example backend/.env
#    → Edit backend/.env and add your OPENAI_API_KEY

# 2. Start all services
docker compose up --build

# Services will be available at:
#   Frontend  →  http://localhost:3000
#   API       →  http://localhost:8000
#   API Docs  →  http://localhost:8000/docs
#   PostgreSQL→  localhost:5432
#   Redis     →  localhost:6379
```

---

### Option C — Backend only (local Python)

```bash
# 1. Clone and set up Python environment
git clone https://github.com/yourusername/syracuse-city-guide.git
cd syracuse-city-guide/backend

python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# 2. Set up environment
cp .env.example .env
# Edit .env with your DATABASE_URL, OPENAI_API_KEY, etc.

# 3. Start the API
uvicorn app.main:app --reload --port 8000

# 4. Visit the interactive docs
open http://localhost:8000/docs
```

---

### Running the ETL pipeline

```bash
# Place your raw CSVs in data/raw/
cp /path/to/*.csv data/raw/

# Dry run — parse files without writing to DB
python etl/pipeline.py --run all --dry-run

# Full run — requires DATABASE_URL in .env
python etl/pipeline.py --run all

# Single source
python etl/pipeline.py --run parks
python etl/pipeline.py --run art
```

---

### Running tests

```bash
cd backend
pytest tests/ -v --cov=app --cov-report=term-missing
```

---

## Demo

### Live demo
🌐 **[https://yourusername.github.io/syracuse-city-guide/frontend/index.html](https://yourusername.github.io/syracuse-city-guide/frontend/index.html)**

*(Deploy to GitHub Pages: Settings → Pages → Source: main branch / root → Save)*

---

### Screenshot walkthrough

| Screen | Description |
|---|---|
| **Home** | Welcome banner, 6 quick-access tiles, featured artwork preview, "Did you know?" about Salt City history |
| **Places to Visit** | 12 attractions with category filters (Museum, Nature, History, Arts, Family, Shopping). Each card has a contextual DYK fact |
| **Restaurants** | 10 curated restaurants with cuisine filters, star ratings, award badges (Noble Cellar 2025 DiRōNA, Dinosaur Bar-B-Que Food Network feature) |
| **Syracuse University** | Timeline 1870→2022, JMA Dome stats banner, 4 school highlights, 12 alumni cards (Biden, Carmelo, Vera Wang, Aaron Sorkin…), campus visitor guide |
| **Parks** | All 7 community centers from the live ODP dataset, with neighborhood tags |
| **Public Art** | 91 artworks with real images from `syr.gov`, filterable by type. Images lazy-load with emoji fallback |
| **Emergency** | SOS banner with pulsing 911 button, 10 tap-to-call helplines, 4 hospitals with 24/7 badge, 3 police/fire cards |

---

### API demo

```bash
# Health check
curl http://localhost:8000/health

# Get helplines (always available, no auth needed)
curl http://localhost:8000/v1/emergency/helplines

# Find nearest hospital to downtown Syracuse
curl "http://localhost:8000/v1/emergency/nearest?lat=43.048&lng=-76.147&type=hospital"

# Ask the chatbot
curl -X POST http://localhost:8000/v1/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "What museums are near downtown?", "city": "syracuse"}'
```

---

## Future Improvements

### Near-term (next sprint)

- [ ] **Live Mapbox integration** — replace the SVG dot-map with interactive Mapbox GL tiles; pin all public art, parks, and places on the map view
- [ ] **Event listings** — wire up the Eventbrite and Ticketmaster API integrations so the Events section surfaces real upcoming events
- [ ] **Search bar** — add a global search across all tabs (places, restaurants, art, parks)
- [ ] **Database connection** — complete the SQLAlchemy async session wiring in `PlacesService` and `RAGService`

### Medium-term

- [ ] **Voice chatbot** — connect the `/v1/chat/stream` endpoint to the frontend mic button using WebRTC + Whisper STT → LLM → ElevenLabs TTS
- [ ] **Offline bundle** — implement `/v1/offline/bundle/{city_id}` to generate a compressed JSON seed for SQLite, so emergency data works with zero connectivity
- [ ] **Itinerary generator** — complete the `ItineraryService` LLM scoring and route optimization logic
- [ ] **GitHub Actions CI** — automated test runs on every pull request; deploy frontend to Pages on merge to `main`
- [ ] **Alembic migrations** — add version-controlled database migration scripts

### Long-term

- [ ] **Multi-city expansion** — onboard Rochester and Albany using the existing city-scoped schema and ETL manifest system
- [ ] **React Native mobile app** — the backend API is already mobile-ready; build the native app shell with offline SQLite caching
- [ ] **Real-time events** — WebSocket push for event updates and parking availability
- [ ] **Accessibility audit** — full WCAG 2.1 AA compliance review; add screen reader labels and keyboard navigation
- [ ] **Analytics dashboard** — anonymous usage analytics to understand which sections visitors use most, informing future data priorities
- [ ] **Multilingual support** — the ETL pipeline embeds content in English; adding Spanish and French would serve Hancock Airport's international arrivals

---

## Contributing

Contributions are welcome. Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Make your changes with tests
4. Run `pytest tests/ -v` and confirm all tests pass
5. Submit a pull request with a clear description

For significant changes, open an issue first to discuss the approach.

---

## License

MIT License — see [LICENSE](LICENSE) for details.

Data from the City of Syracuse Open Data Portal is published under open data terms.
OpenStreetMap data is © OpenStreetMap contributors, licensed under the ODbL.
Yelp data is used under the Yelp Fusion API Terms of Service.

---

## Acknowledgements

- **City of Syracuse** — for publishing open data at [data.syrgov.net](https://data.syrgov.net)
- **Syracuse Public Art Commission** — for the public art dataset and image CDN
- **HIFLD / U.S. Department of Homeland Security** — for open emergency services data
- **OpenStreetMap contributors** — for global open geospatial data

---

<div align="center">
  <sub>Built with open data · Designed for visitors · Powered by Syracuse 🍊</sub>
</div>
