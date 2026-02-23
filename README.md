TOR Market Intelligence Platform
Technical White Paper

Version: 1.0
Status: Architecture Definition
Scope: Personal Use → Scalable TOR Service

1. Executive Summary

This project defines a two-application platform designed to collect, normalize, and present vendor product pricing data originating exclusively from TOR (.onion) marketplaces.

The system consists of:

Tool 1 — Data Ingest Platform

A modular crawler and scraper responsible for:

Navigating TOR marketplaces

Extracting product listings

Normalizing pricing and volume data

Persisting structured market intelligence

Supporting manual captcha resolution

Operating continuously on a VM

Tool 2 — TOR Catalog Website

A separate read-only presentation layer providing:

Clean product comparison views

Category organization

Value analysis (price per volume)

Vendor comparison

User-submitted marketplace discovery

Mirror-aware link handling

The two systems remain strictly decoupled.

2. System Philosophy

This platform is not a scraper.

It is a:

Modular TOR-native Market Intelligence System

Key principles:

Isolation of responsibilities

Failure tolerance

Human-assisted automation

Site-specific parsing

Data normalization first

Presentation independent from collection

3. System Overview
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
                       PostgreSQL DB
                              │
                ┌─────────────▼────────────┐
                │   TOR CATALOG WEBSITE    │
                │      (Read Only)         │
                └──────────────────────────┘
4. Tool 1 — Data Ingest Platform
4.1 Purpose

Continuously discover, crawl, and extract vendor product data from TOR sites and store it in a structured database suitable for analytics.

4.2 Core Responsibilities
Navigation

Crawl category pages

Discover product listings

Handle pagination automatically

Recover from navigation failures

Extraction

Collect:

Product name

Vendor name

Price

Currency

Volume / weight

Listing URL

Category

Timestamp

Site source

Normalization

All data standardized into comparable units.

Examples:

Raw Value	Stored
1oz	28.349g
1000mg	1g
3.5 grams	3.5g
4.3 Technology Stack
Backend

Python

Browser Automation

Playwright

Network Routing

TOR daemon (SOCKS5 proxy)

Database

PostgreSQL

Local Control UI

FastAPI

HTMX (zero JavaScript philosophy)

4.4 TOR Integration

Browser traffic routes through:

Tor daemon → SOCKS5 proxy → Playwright browser

Advantages:

User-visible browsing

Manual captcha solving

Stable automation

Identical anonymity properties

4.5 Modular Site Adapter System

Each marketplace receives its own module.

/sites/
    base_site.py
    vendor_alpha.py
    vendor_beta.py
Required Adapter Methods

starting URLs

product discovery

product extraction

pagination detection

captcha detection

failure handling

Adding support for a new site requires zero engine modification.

4.6 Crawl Engine
Queue-Driven Model

All navigation stored as jobs:

crawl_jobs
-----------
url
site
status
retry_count
last_attempt

Benefits:

Crash recovery

Pause/resume

Parallel crawling

Auditability

4.7 Captcha System

All sites are assumed to require captcha interaction.

Phase 1 (Initial Design)

Manual resolution:

Scraper pauses

UI displays captcha state

User solves captcha

User presses Resume

Crawl continues

Future Extension (Reserved)

AI captcha solvers

External solving APIs

Behavioral automation

Architecture includes plugin system:

captcha_handlers/
4.8 Failure Tolerance

The crawler never stops globally.

Rules:

Extraction failure → log → continue

Timeout → retry

Broken page → abandon safely

Site outage → pause site only

4.9 Data Model
Products

Stores canonical listing.

Fields include:

normalized weight

normalized pricing

site origin

vendor identity

hash fingerprint

Price History

Every scrape creates a record.

Enables:

price tracking

deal detection

trend analysis

Vendor Table

Stores vendor identity independent of listings.

Category Table

Allows cross-site grouping.

4.10 Reserved Expansion — Reviews (V2)

The architecture intentionally leaves space for:

Vendor reputation scores

Product reviews

Rating aggregation

Sentiment analysis

Cross-market reputation linking

Review ingestion will become a secondary crawler pipeline.

5. Tool 2 — TOR Catalog Website
5.1 Purpose

Provide a clean, fast, private comparison interface accessible exclusively through TOR.

This service NEVER scrapes.

It reads database only.

5.2 Design Philosophy

No JavaScript required

Server-rendered pages

Extremely lightweight

Compatible with hardened TOR browsers

5.3 Technology Stack

FastAPI

HTMX

Server templates

PostgreSQL read-only connection

TOR Hidden Service

5.4 Primary Features
Category Browsing

Users view products grouped by category.

Default Sorting (Core Feature)

All listings sorted automatically by:

Price per Volume (Value Index)

Cheapest value at top → most expensive bottom.

Dynamic Price Competition Grids

System groups products whose value index falls within:

±10% price-per-volume window

Result:

Users instantly see:

Direct price competitors

This visually exposes market pricing tiers.

Filtering Options

Category

Vendor

Weight range

Price range

Value ranking

Newly added listings

Recently reduced prices

Direct Vendor Navigation

Each listing provides:

clickable link

segmented link

mirror-compatible navigation

5.5 Mirror-Aware Link System

Users may possess private mirrors.

System stores:

.onion base removed path

Example storage:

/product/abc123

Display options:

Full known onion link

Path-only link for custom mirrors

Copy-to-clipboard segment

User pastes onto personal mirror.

5.6 User URL Submission System

Users can submit new marketplace URLs.

System behavior:

Store submission

Normalize URL

Remove duplicates

Queue for manual review

Optional auto-validation later

Database maintains unique marketplace registry.

5.7 Privacy Principles

The website:

performs no tracking

stores no analytics cookies

executes no client JS

leaks no external requests

6. Deployment Model
Data Ingest VM

Continuous operation

Tor service

Database write access

Local admin UI

Viewer Server

Read-only DB

TOR hidden service

Public access

Separation protects scraping infrastructure.

7. Security Design

Ingest system never public-facing

Viewer cannot control crawler

DB permission separation

No remote execution paths

TOR isolation per service

8. Scaling Strategy

Future growth supports:

additional marketplaces

distributed crawlers

regional TOR nodes

scheduled refresh crawling

price alerts

deal notifications

9. Advanced Future Roadmap
Phase 2

Review scraping

Vendor reputation scoring

Phase 3

Price anomaly detection

Automatic deal highlighting

Phase 4

Multi-node crawler cluster

Phase 5

Predictive pricing analytics

10. Key Advantages

This architecture enables:

resilient TOR scraping

modular expansion

human-assisted automation

consistent product comparison

anonymous catalog browsing

The system evolves from personal tooling into a full market intelligence platform without redesign.

11. Conclusion

The platform establishes:

a structured TOR data ingestion engine

a privacy-respecting comparison catalog

a future-ready analytical framework

By separating ingestion from presentation, the system remains maintainable, scalable, and operationally safe while supporting increasingly sophisticated analysis capabilities.
