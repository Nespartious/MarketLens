# 🧅 Onionomics — TOR Market Intelligence Platform

![Status](https://img.shields.io/badge/status-architecture-blue)
![Stage](https://img.shields.io/badge/stage-active%20development-orange)
![Python](https://img.shields.io/badge/python-3.11+-green)
![License](https://img.shields.io/badge/license-private-lightgrey)
![TOR](https://img.shields.io/badge/network-TOR-purple)
![JS](https://img.shields.io/badge/frontend-zero%20javascript-black)

---

# 🧠 Overview

**Onionomics** is a modular **TOR-native market intelligence system** designed to:

- Crawl `.onion` marketplaces
- Normalize vendor pricing data
- Build a structured analytics database
- Present price/value comparisons through a clean TOR website

This is **NOT** a simple scraper.

It is a long-term **data intelligence platform**.

---

# ⚙️ System Architecture

            ┌──────────────────────────┐
            │     TOR MARKETPLACES     │
            └─────────────┬────────────┘
                          │
                 TOR Network (.onion)
                          │
            ┌─────────────▼────────────┐
            │   DATA INGEST PLATFORM   │
            │    Crawler / Scraper     │
            └─────────────┬────────────┘
                          │
                   PostgreSQL Database
                          │
            ┌─────────────▼────────────┐
            │     TOR CATALOG SITE     │
            │        Read Only         │
            └──────────────────────────┘

Two independent applications:

| Tool | Purpose |
|---|---|
| **Data Ingest** | Crawl + normalize market data |
| **Catalog Website** | Present analytics safely |

---

# 🧱 Core Philosophy

- ✔ Modular site adapters
- ✔ Human-assisted automation
- ✔ Failure tolerant crawling
- ✔ Zero-JS TOR compatibility
- ✔ Data normalization first
- ✔ Presentation isolated from ingestion

> Separation of concerns = long-term survivability.

---

# 🛠 Tool 1 — Data Ingest Platform

## Purpose

Continuously discover and ingest marketplace data into a structured database.

---

## Responsibilities

### Navigation
- Crawl category pages
- Discover product listings
- Handle pagination
- Resume after failure

### Extraction
Collect:

- Product Name
- Vendor
- Price
- Currency
- Weight / Volume
- Listing URL
- Category
- Timestamp
- Source Marketplace

---

### Normalization

All values converted into comparable units.

| Raw | Stored |
|---|---|
| 1oz | 28.349g |
| 1000mg | 1g |
| 3.5 grams | 3.5g |

---

## Technology Stack

| Layer | Technology |
|---|---|
| Backend | Python |
| Automation | Playwright |
| Routing | TOR SOCKS5 |
| Database | PostgreSQL |
| Control UI | FastAPI + HTMX |

---

## TOR Integration


Tor Daemon → SOCKS5 Proxy → Playwright Browser


Advantages:

- Visible browsing session
- Manual captcha solving
- Stable automation
- True TOR identity parity

---

## Modular Site Adapter System


/sites/
base_site.py
example_market.py


Each site defines:

- start URLs
- product discovery
- extraction rules
- pagination logic
- captcha detection

Adding new marketplaces requires **no engine changes**.

---

## Crawl Engine

Queue-driven architecture:

crawl_jobs

url
site
status
retry_count
last_attempt


Capabilities:

- Pause / Resume
- Crash recovery
- Parallel workers
- Full audit history

---

## Captcha Handling

### Phase 1 — Manual

1. Crawl pauses
2. User solves captcha in browser
3. User presses **Resume**
4. Crawl continues

Plugin system reserved:


captcha_handlers/


Future:
- AI solvers
- External APIs
- Behavioral automation

---

## Failure Tolerance

Crawler rules:

- Extraction failure → log + continue
- Timeout → retry
- Page error → abandon safely
- Site outage → isolate failure

Crawler **never stops globally**.

---

## Data Model

### Products
Canonical listing data.

- normalized weight
- normalized price
- vendor identity
- site origin
- fingerprint hash

### Price History
Every scrape recorded.

Enables:
- trend tracking
- deal detection
- analytics

### Vendors
Independent vendor identity table.

### Categories
Cross-site grouping.

---

## Reserved Expansion (V2)

- Vendor reviews
- Product ratings
- Sentiment analysis
- Reputation scoring

Architecture already prepared.

---

# 🌐 Tool 2 — TOR Catalog Website

## Purpose

Provide a **fast, private comparison interface** accessible only through TOR.

⚠️ This service **never scrapes**.

Database read-only.

---

## Design Principles

- No JavaScript
- Server-rendered HTML
- Ultra lightweight
- Hardened TOR compatibility

---

## Stack

- FastAPI
- HTMX
- Server templates
- PostgreSQL (read-only)
- TOR Hidden Service

---

## Primary Features

### Category Views
Products grouped automatically.

---

### Default Sorting — Value Index


Price per Volume


Cheapest value → Most expensive.

---

### Dynamic Competition Grids

Products grouped when within:


±10% value difference


Shows direct price competitors instantly.

---

### Filters

- Category
- Vendor
- Weight range
- Price range
- New listings
- Price drops
- Value ranking

---

### Mirror-Aware Links

System stores only path:


/product/abc123


Users may:

- open known mirror
- append to private mirror
- copy segmented link

---

### Marketplace Submission System

Users may submit new onion URLs.

System will:

- normalize URLs
- remove duplicates
- queue for review
- maintain marketplace registry

---

## Privacy Guarantees

- No tracking
- No analytics cookies
- No client JS
- No external requests

---

# 🚀 Deployment Model

## Data Ingest VM

- Continuous crawler
- TOR service
- DB write access
- Local admin interface

## Viewer Server

- Read-only DB
- Public TOR hidden service

Separation protects crawler infrastructure.

---

# 🔐 Security Model

- Ingest never public-facing
- Website cannot control crawler
- Database role separation
- No remote execution surface
- TOR isolation per service

---

# 📦 Repository Structure


onionomics/
│
├── ingest/
│ ├── crawler/
│ ├── sites/
│ ├── captcha_handlers/
│ └── workers/
│
├── catalog/
│ ├── web/
│ ├── templates/
│ └── routes/
│
├── database/
│ ├── migrations/
│ └── models/
│
├── infra/
│ ├── docker/
│ └── tor/
│
└── docs/


---

# ⚡ Quick Start (Planned)

## Requirements

- Docker
- Docker Compose
- TOR
- Python 3.11+

---

## Start Environment


git clone <repo>
cd onionomics
docker compose up


---

## Services

| Service | Port |
|---|---|
| Ingest UI | localhost:8000 |
| Catalog Site | TOR Hidden Service |
| PostgreSQL | internal |

---

# 🧭 Development Roadmap

## Phase 1
- [x] Architecture design
- [ ] Base crawler engine
- [ ] First marketplace adapter
- [ ] Manual captcha workflow
- [ ] Database normalization

## Phase 2
- [ ] Catalog TOR website
- [ ] Value index sorting
- [ ] Competition grids

## Phase 3
- [ ] Review ingestion
- [ ] Vendor reputation scoring

## Phase 4
- [ ] Distributed crawler nodes
- [ ] Multi-VM scaling

## Phase 5
- [ ] Predictive price analytics
- [ ] Deal alerting engine

---

# 🤝 Contribution Model

Development follows:

- modular adapters
- isolated components
- reproducible crawling
- privacy-first design

New site integrations occur via:


sites/<market_name>.py


---

# ⭐ Key Advantages

- Resilient TOR scraping
- Human-assisted automation
- Modular expansion
- Anonymous browsing
- Market intelligence analytics

This system evolves from a **personal research tool** into a full **TOR market analytics platform** without redesign.

---

# 📜 License

Private / Internal Development

---

# 🧅 Onionomics

> Turning hidden markets into structured intelligence.
