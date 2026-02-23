# 🧅 TOR Market Intelligence Platform
## Technical White Paper

**Version:** 1.0  
**Status:** Architecture Definition  
**Scope:** Personal Use → Scalable TOR Service  

---

# 1. Executive Summary

This project defines a **two-application platform** designed to collect, normalize, and present vendor product pricing data originating exclusively from **TOR (.onion) marketplaces**.

The system consists of:

---

## 🛠 Tool 1 — Data Ingest Platform

A modular crawler and scraper responsible for:

- [x] Navigating TOR marketplaces
- [x] Extracting product listings
- [x] Normalizing pricing and volume data
- [x] Persisting structured market intelligence
- [x] Supporting manual captcha resolution
- [x] Operating continuously on a VM

---

## 🌐 Tool 2 — TOR Catalog Website

A separate **read-only presentation layer** providing:

- Clean product comparison views
- Category organization
- Value analysis *(price per volume)*
- Vendor comparison
- User-submitted marketplace discovery
- Mirror-aware link handling

> ✅ The two systems remain **strictly decoupled**.

---

# 2. System Philosophy

This platform is **not a scraper**.

It is a:

> **Modular TOR-Native Market Intelligence System**

### Core Principles

- Isolation of responsibilities
- Failure tolerance
- Human-assisted automation
- Site-specific parsing
- Data normalization first
- Presentation independent from collection

---

            ┌──────────────────────────┐
            │   TOR MARKETPLACES       │
            └─────────────┬────────────┘
                          │
                 TOR Network (.onion)
                          │
            ┌─────────────▼────────────┐
            │  DATA INGEST PLATFORM    │
            │  (Crawler / Scraper)     │
            └─────────────┬────────────┘
                          │
                   PostgreSQL Database
                          │
            ┌─────────────▼────────────┐
            │   TOR CATALOG WEBSITE    │
            │        (Read Only)       │
            └──────────────────────────┘

# 3. System Overview



---

# 4. Tool 1 — Data Ingest Platform

---

## 4.1 Purpose

Continuously discover, crawl, and extract vendor product data from TOR sites and store it in a structured analytics database.

---

## 4.2 Core Responsibilities

### Navigation

- Crawl category pages
- Discover product listings
- Handle pagination automatically
- Recover from navigation failures

---

### Extraction

Collected data:

- Product name
- Vendor name
- Price
- Currency
- Volume / weight
- Listing URL
- Category
- Timestamp
- Site source

---

### Normalization

All values converted into standardized units.

| Raw Value | Stored |
|---|---|
| 1oz | 28.349g |
| 1000mg | 1g |
| 3.5 grams | 3.5g |

---

## 4.3 Technology Stack

### Backend
- Python

### Browser Automation
- Playwright

### Network Routing
- TOR daemon (SOCKS5 proxy)

### Database
- PostgreSQL

### Local Control UI
- FastAPI
- HTMX *(zero JavaScript philosophy)*

---

## 4.4 TOR Integration

Tor daemon → SOCKS5 proxy → Playwright browser

### Advantages

- User-visible browsing
- Manual captcha solving
- Stable automation
- True TOR anonymity

---

## 4.5 Modular Site Adapter System

Each marketplace is implemented as its own module.
/sites/
base_site.py
vendor_alpha.py
vendor_beta.py


### Required Adapter Capabilities

- Starting URLs
- Product discovery
- Product extraction
- Pagination detection
- Captcha detection
- Failure handling

✅ Adding new sites requires **zero engine modification**.

---

## 4.6 Crawl Engine

### Queue-Driven Model

crawl_jobs

url
site
status
retry_count
last_attempt



### Benefits

- Crash recovery
- Pause / Resume
- Parallel crawling
- Full audit trail

---

## 4.7 Captcha System

All TOR sites are assumed to require captcha interaction.

### Phase 1 — Manual Resolution

1. Scraper pauses
2. UI displays captcha state
3. User solves captcha
4. User presses **Resume**
5. Crawl continues

---

### Future Extensions (Reserved)

- AI captcha solvers
- External solving APIs
- Behavioral automation


captcha_handlers/

---

## 4.8 Failure Tolerance

The crawler **never stops globally**.

Rules:

- Extraction failure → log → continue
- Timeout → retry
- Broken page → abandon safely
- Site outage → pause site only

---

## 4.9 Data Model

### Products

Stores canonical listing data:

- normalized weight
- normalized pricing
- site origin
- vendor identity
- hash fingerprint

---

### Price History

Each scrape generates a record enabling:

- Price tracking
- Deal detection
- Trend analysis

---

### Vendor Table
Stores vendor identity independent of listings.

### Category Table
Supports cross-site grouping.

---

## 4.10 Reserved Expansion — Reviews (V2)

Architecture reserves support for:

- Vendor reputation scores
- Product reviews
- Rating aggregation
- Sentiment analysis
- Cross-market reputation linking

Review ingestion will become a **secondary crawler pipeline**.

---

# 5. Tool 2 — TOR Catalog Website

---

## 5.1 Purpose

Provide a **clean, fast, private comparison interface** accessible exclusively via TOR.

> ⚠️ This service **never scrapes**.  
> It reads database data only.

---

## 5.2 Design Philosophy

- No JavaScript required
- Server-rendered pages
- Extremely lightweight
- Compatible with hardened TOR browsers

---

## 5.3 Technology Stack

- FastAPI
- HTMX
- Server templates
- PostgreSQL (read-only)
- TOR Hidden Service

---

## 5.4 Primary Features

### Category Browsing
Products grouped by category.

---

### Default Sorting (Core Feature)

Listings automatically sorted by:

Price per Volume (Value Index)


Cheapest value → Most expensive.

---

### Dynamic Price Competition Grids

Products grouped when within:

±10% price-per-volume window

Result:

> Immediate visibility of direct price competitors.

---

### Filtering Options

- Category
- Vendor
- Weight range
- Price range
- Value ranking
- Newly added listings
- Recently reduced prices

---

### Direct Vendor Navigation

Each listing provides:

- Clickable link
- Segmented link
- Mirror-compatible navigation

---

## 5.5 Mirror-Aware Link System

Users may use private mirrors.

System stores:


.onion base removed path

Example:/product/abc123


### Display Options

- Full known onion link
- Path-only link for custom mirrors
- Copy-ready URL segment

---

## 5.6 User URL Submission System

Users can submit marketplace URLs.

System behavior:

- Store submission
- Normalize URL
- Remove duplicates
- Queue for manual review
- Optional automated validation

Maintains a unique marketplace registry.

---

## 5.7 Privacy Principles

The website:

- performs no tracking
- stores no analytics cookies
- executes no client JavaScript
- leaks no external requests

---

# 6. Deployment Model

## Data Ingest VM

- Continuous operation
- TOR service
- Database write access
- Local admin UI

---

## Viewer Server

- Read-only database
- TOR hidden service
- Public access

✅ Separation protects scraping infrastructure.

---

# 7. Security Design

- Ingest system never public-facing
- Viewer cannot control crawler
- Database permission separation
- No remote execution paths
- TOR isolation per service

---

# 8. Scaling Strategy

Future expansion supports:

- Additional marketplaces
- Distributed crawlers
- Regional TOR nodes
- Scheduled refresh crawling
- Price alerts
- Deal notifications

---

# 9. Advanced Future Roadmap

## Phase 2
- Review scraping
- Vendor reputation scoring

## Phase 3
- Price anomaly detection
- Automatic deal highlighting

## Phase 4
- Multi-node crawler cluster

## Phase 5
- Predictive pricing analytics

---

# 10. Key Advantages

This architecture enables:

- Resilient TOR scraping
- Modular expansion
- Human-assisted automation
- Consistent product comparison
- Anonymous catalog browsing

The system evolves from personal tooling into a full market intelligence platform **without redesign**.

---

# 11. Conclusion

The platform establishes:

- A structured TOR data ingestion engine
- A privacy-respecting comparison catalog
- A future-ready analytical framework

By separating ingestion from presentation, the system remains maintainable, scalable, and operationally safe while supporting increasingly sophisticated analysis capabilities.
