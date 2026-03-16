# 🍊 Syracuse City Guide

> An AI-powered visitor guide and open data dashboard for Syracuse, New York — built using real datasets from the City of Syracuse Open Data Portal.
> Note: This repository includes only sample datasets for demonstration.
Full datasets are available from the City of Syracuse Open Data Portal and other public APIs.

---

## 📌 Live Demo

Once hosted on GitHub Pages, the files are accessible at:

```
https://yourusername.github.io/syracuse-city-guide/syracuse_guide_v2.html
https://yourusername.github.io/syracuse-city-guide/syracuse_dashboard.html
```

> Replace `yourusername` with your actual GitHub username.

---

## 📁 Project Structure

```
syracuse-city-guide/
├── README.md
├── syracuse_guide_v2.html           ← Main tourist guide (recommended entry point)
├── syracuse_tourist_guide.html      ← Earlier version of tourist guide
├── syracuse_dashboard.html          ← Open data analyst dashboard
└── data/
    ├── Syracuse_Public_Art_NEW.csv
    ├── Syracuse_Parks_26_Recreation_Community_Centers.csv
    ├── Crime_Data_2025_Part_1_Offenses.csv
    ├── Emergency_Snow_Routes.csv
    ├── Syracuse_Parcel_Map_2025_Q4.csv
    └── Syracuse_Common_Council_Boundaries_2023.csv
```

---

## 🗂️ Files Overview

### `syracuse_guide_v2.html` — Tourist Guide (Full Version)
The primary visitor-facing application. A warm, welcoming city guide designed for tourists arriving in Syracuse.

**Tabs included:**
| Tab | Contents |
|-----|----------|
| 🏠 Home | Welcome screen with quick-access cards and featured art |
| 🗺️ Places to Visit | 12 curated attractions with filterable categories |
| 🍽️ Restaurants | 10 top restaurants with awards, ratings, and cuisine filters |
| 🍊 Syracuse University | Full SU history, timeline, JMA Dome facts, notable alumni |
| 🌳 Parks | 7 community parks & recreation centers from the city dataset |
| 🎨 Public Art | 91 mapped artworks with real images from the city's servers |
| 🚨 Emergency & Help | Helplines, hospitals, police, fire — all tap-to-call |

**"Did You Know?" facts** are embedded throughout every section — tiny, curious facts about Syracuse history, culture, and the university.

---

### `syracuse_dashboard.html` — Open Data Dashboard
An analyst-facing dashboard visualising all 6 city datasets with interactive charts, filtered tables, and SVG maps.

**Tabs included:**
| Tab | Contents |
|-----|----------|
| Overview | Stat strip, donut charts, bar charts, council district population |
| Public Art | Filterable card grid with real artwork images |
| Parks | Park listings with interactive SVG map |
| Crime | Jan 2025 Part 1 offences with filter and map |
| Snow Routes | 3,685 segments, 112 priority streets listed |
| Parcels | Land use distribution, neighborhood breakdowns |

---

### `syracuse_tourist_guide.html` — Tourist Guide (v1)
An earlier, simpler version of the tourist guide. Contains parks, public art, and the emergency section. Superseded by `syracuse_guide_v2.html`.

---

## 📊 Data Sources

All primary data is sourced from the **City of Syracuse Open Data Portal** ([data.syrgov.net](https://data.syrgov.net)).

| File | Description | Records | Source |
|------|-------------|---------|--------|
| `Syracuse_Public_Art_NEW.csv` | 108 public artworks — murals, sculptures, monuments, mosaics. Includes artist, media, coordinates, and image URLs. | 108 | Syracuse Open Data Portal |
| `Syracuse_Parks_26_Recreation_Community_Centers.csv` | 7 community parks with recreation centers. Name, address, and GPS coordinates. | 9 | Syracuse Open Data Portal |
| `Crime_Data_2025_Part_1_Offenses.csv` | January 2025 Part 1 offences (FBI UCR classification). Includes crime type, address, coordinates, and arrest status. | 33 | Syracuse Open Data Portal |
| `Emergency_Snow_Routes.csv` | City-designated priority snow clearance routes. 3,685 road segments across 112 named streets. | 3,685 | Syracuse Open Data Portal |
| `Syracuse_Parcel_Map_2025_Q4.csv` | Full city parcel map, Q4 2025. Includes land use classification, owner info, assessed value, neighborhood, and GPS coordinates. | 41,263 | Syracuse Open Data Portal |
| `Syracuse_Common_Council_Boundaries_2023.csv` | 5 Common Council district boundaries with population, voting-age population, and demographic breakdown. | 5 | Syracuse Open Data Portal |

### External / Supplementary Data
The tourist guide also uses the following data, embedded directly in the HTML:

| Category | Source |
|----------|--------|
| Restaurants | Curated from Yelp, OpenTable, and local media (2024–2025) |
| Places to visit | Curated from VisitSyracuse.com and venue websites |
| Emergency contacts | City of Syracuse, Onondaga County, and national helplines |
| SU history & alumni | Syracuse University official records and Wikipedia |

---

## 🏗️ Architecture

Both applications are **single-file, zero-dependency HTML** — no build tools, no npm, no server required.

```
┌─────────────────────────────────────────────┐
│                Single HTML File              │
│                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │   CSS    │  │   HTML   │  │    JS    │  │
│  │ (inline) │  │ sections │  │ (inline) │  │
│  └──────────┘  └──────────┘  └──────────┘  │
│                                             │
│  Data: embedded as JS constants             │
│  Fonts: Google Fonts CDN                    │
│  No external JS frameworks                  │
└─────────────────────────────────────────────┘
```

**Why single-file?**
- Works offline once loaded (important for airport WiFi use case)
- No server or hosting infrastructure needed beyond static file serving
- Easy to share, email, or deploy to any CDN

---

## 🚀 How to Use

### Option 1 — Open locally
Download any `.html` file and open it directly in a browser. No internet required after the page loads (fonts may not render without connection, but all content works).

### Option 2 — GitHub Pages (recommended)
1. Fork or clone this repository
2. Go to **Settings → Pages**
3. Set source to **Deploy from a branch → main → / (root)**
4. Your site will be live at `https://yourusername.github.io/syracuse-city-guide/`

### Option 3 — Any static host
Upload the HTML files to Netlify, Vercel, Cloudflare Pages, or any web server. No configuration needed.

---

## 🔮 Roadmap — Future Development

This project was designed with expansion in mind. Suggested next steps:

- [ ] **Backend API** — FastAPI + PostgreSQL/PostGIS to serve live data instead of embedded JSON
- [ ] **RAG chatbot** — Voice-enabled AI assistant using the datasets as a knowledge base
- [ ] **Multi-city support** — The schema and architecture support adding Rochester, Albany, Buffalo with a config file change
- [ ] **Real-time events** — Eventbrite and Ticketmaster API integration for live event listings
- [ ] **Restaurants via Yelp Fusion API** — Replace curated list with live data
- [ ] **Offline PWA** — Convert to a Progressive Web App with service worker caching
- [ ] **Native mobile app** — React Native wrapper using the same data pipeline

See the [full system architecture document](#) for the complete technical design including the RAG pipeline, PostGIS schema, and multi-city scalability plan.

---

## 🏙️ About Syracuse

Syracuse is a mid-sized city in Central New York, seat of Onondaga County. Known historically as **"The Salt City"** for its dominant 19th-century salt production industry that helped fund the Erie Canal, today Syracuse is home to:

- **Syracuse University** (founded 1870) — a top-50 research university and home of the Orange
- **The JMA Wireless Dome** — largest on-campus domed stadium in the United States
- **The Erie Canal Museum** — commemorating the canal that transformed American commerce
- **A rich public art scene** — 108 artworks spanning every neighbourhood
- **Tipperary Hill** — home to the world's only traffic light with green on top

---

## 📜 Licence & Attribution

**Data licence:** Datasets from the City of Syracuse Open Data Portal are made available under the [Open Data Commons Attribution Licence (ODC-By)](https://opendatacommons.org/licenses/by/).

**Attribution required:** If you use or redistribute this data, please credit:
> Data sourced from the City of Syracuse Open Data Portal — [data.syrgov.net](https://data.syrgov.net)

**Code:** This project's HTML, CSS, and JavaScript are available under the [MIT Licence](LICENSE).

---

## 🤝 Contributing

Contributions welcome. If you spot outdated information, want to add a new attraction, or want to help build the backend API:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/add-restaurants-api`
3. Commit your changes: `git commit -m 'Add Yelp Fusion integration'`
4. Push and open a Pull Request

---

## 👤 Author

Built as part of a civic technology project exploring how open data from the City of Syracuse can power real visitor experiences.

---

*Last updated: March 2025 · Data current as of Q4 2025*
