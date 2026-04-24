# Cyber Threat Intelligence Data ETL Pipelines

An end-to-end modular Data Engineering platform (ETL) developed in Python. This project extracts, transforms, and stores real-time cybersecurity intelligence data regarding actively exploited vulnerabilities and live malware distribution.

## About the Project

This project was built to practice Data Engineering skills and Data Platform architecture. What started as a single ETL script has evolved into a unified pipeline processing two distinct critical security feeds:

1. **CISA KEV Catalog** — Tracks worldwide known exploited vulnerabilities.
2. **Abuse.ch URLhaus** — Tracks live, actively distributing malware and botnets.

## Architecture & Data Flow

The architecture follows a modular ETL approach, allowing jobs to run independently or orchestrated centrally.

- **Extract:** HTTP requests (via `requests`) to public and authenticated REST APIs, passing auth keys via headers.
- **Transform:** Unstructured JSON processed with `pandas`, including:
  - Filtering analytical columns and handling null/missing values
  - Data type conversion and standardization (`datetime` parsing)
  - **Business Logic & Feature Engineering:** Boolean masks to drop offline threats; lambda functions to classify active threats by severity (`CRITICAL` vs `HIGH`)
- **Load:** Cleaned DataFrames pushed into a **PostgreSQL** cloud database (Neon), with automatic schema generation and updates for analytical SQL queries.

## Tech Stack

| Layer | Tool |
|---|---|
| Language | Python 3 |
| Data Manipulation | Pandas |
| Database | PostgreSQL (Neon) + SQLAlchemy |
| HTTP Requests | Requests |
| Environment | python-dotenv |

## How to Run

### 1. Clone and install dependencies

```bash
git clone https://github.com/leogttrrs/cyber-threat-intel-pipelines.git
cd cyber-threat-intel-pipelines
pip install -r requirements.txt
```

### 2. Configure environment variables

Copy `.env.example`, rename it to `.env`, and fill in your credentials:

```env
DATABASE_URL=postgresql://user:password@host...
URLHAUS_API_KEY=your_abuse_ch_api_key_here
```

### 3. Run the pipelines

**Option A — Orchestrated (recommended):** runs all ETL jobs sequentially.

```bash
python main.py
```

**Option B — Individual pipelines:** useful for debugging or isolated updates.

```bash
python ETLs/vulnerabilities_etl.py
# or
python ETLs/malware_etl.py
```

Results will automatically populate the `active_vulnerabilities` and `active_malwares` tables in your NeonDB PostgreSQL database.