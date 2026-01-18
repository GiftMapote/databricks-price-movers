# databricks-price-movers

## Databricks Price Movers (ZAR) — Real-time-ish Crypto Trends in South African Time

## Purpose
Build a production-style data engineering pipeline on **Azure Databricks** that ingests crypto price data, processes it using a **Medallion (Bronze → Silver → Gold) Lakehouse** design, and serves an interactive dashboard.

The dashboard helps you track:
- what is moving **right now**
- how prices **trend over time**
- all displayed in **South African time (Africa/Johannesburg)**
  

## Problem this project solves
Many portfolio projects stop at a single notebook and don’t show real-world data engineering skills such as:
- ingesting data repeatedly on a schedule
- handling schema drift in semi-structured JSON
- building clean analytical tables (Bronze → Silver → Gold)
- supporting both “current view” and “historical trends”
- delivering outputs that non-technical users can consume (dashboards)

This project addresses those gaps by delivering an end-to-end pipeline that:
1) pulls prices every few minutes from a public API  
2) lands raw files in a data lake (ADLS)  
3) ingests raw data into a Bronze Delta table (Auto Loader)  
4) transforms to clean Silver tables (one row per asset per pull)  
5) computes Gold aggregates and movers in SA time  
6) serves a dashboard that can be refreshed and used daily  

## What you can do with the dashboard
- View **hourly trends** per asset (SA time)
- View **top movers** for the latest hour
- Filter by asset (e.g., Ripple) to inspect trends

> Note: movers require at least two time buckets (for example, two hours) to calculate % change.

## Example use case
A trader, analyst, or fintech team can use this dashboard to quickly identify which assets are gaining or losing momentum and review recent movement history without manually checking multiple exchanges or websites.

## Architecture
![Archicture](docs/diagrams/Architecture.png)

## Tech stack
- **Azure Databrics** (notebooks, jobs, Auto Loader, Delta Lake)
- **Azure Data Lake Storage Gen2 (ADLS)** (raw storage)
- **Delta Lake** (Bronze/Silver/Gold tables)
- **Databricks SQL Warehouse** (dashboard queries + visualizations)
- **CoinGecko API** (data source)
- **GitHub** (version control + documentation)

## Data Layers and tables (Medallion)
- **Bronze Layer** : 'bronze_prices_raw' -> raw ingested API records + metadata
- **Silver Layer** : 'silver_prices' -> Normalized rows
- **Gold Laye** : 'gold_hourly_prices' and 'gold_hourly_movers' -> Final Aggregates

## Prerequisites
- Azure Databricks workspace + cluster
- ADLS Gen2 connected
- Unity Catalog
- SQL Warehouse for dashboard

## Notebooks
1. [01_collector_write_raw](notebooks/01_collector_write_raw.ipynb)
2. [02_bronze_autoloader](notebooks/02_bronze_autoloader.ipynb)
3. [03_silver_transform](notebooks/03_silver_transform.ipynb)
4. [04_gold_aggregations](notebooks/04_gold_aggregations.ipynb)

## Dashboard
Published dashboars (may required Databricks workspace access):
https://adb-7405614192573628.8.azuredatabricks.net/dashboardsv3/01f0f46ca0df17268b1cc0f3ef828920/published?o=7405614192573628

## Project screenshots
### 1) Dashboard overview
![Dashboard Overview](docs/screenshots/Dashboard.png)

### 2) Job Schedule Pipeline
![Jobs Schedule](docs/screenshots/jobrun.png)

### 3) Medallion tables (Bronze/ Silver/ Gold
![Tables](docs/screenshots/catalog_view.png)


## How to run (step-by-step)

### 0) Setup
- Ensure ADLS Gen2 is accessible via 'abfss://'
- Choose a Unity Catalog **catalog** and **schema**
- Ensure you have a SQL Warehouse available for dashboards

### 1) Ingest raw data (Collector)
Run: [01_collector_write_raw](notebooks/01_collector_write_raw.ipynb)

What it does:
- Calls CoinGecko API for selected assets in ZAR
- Writes a JSON file to ADLS: 'raw/YYYY-MM-DD/HH/
- Adds metadata like 'pulled_at_utc' and 'pulled_at_sa' timestamps

Validate:
- Check a new file exists on the raw folder

### 2) Bronze ingetion (Auto Loader)
Run: [02_bronze_autoloader](notebooks/02_bronze_autoloader.ipynb)

What it does:
- Uses Auto Loader to ingest raw JSON files into 'bronze_prices_raw'
- Adds 'ingest_ts' and 'source_file'

Validate:
```sql
select count(*) from bronze_prices_raw;
```

### 3) Silver transform
Run: [03_silver_transform](notebooks/03_silver_transform.ipynb)

What is does:
- Converts nested API response into 1 row per asset per pull
- Produces: 'asset_id', 'price', 'event_ts' (UTC) and 'event_ts_sa' (SA time)

Validate:
```sql
 select asset_id, max(event_ts_sa) from silver_prices group by assets_id;
```

### 4) Gold Aggregates
Run: [04_gold_aggregations](notebooks/04_gold_aggregations.ipynb)

What it does:
- Creates agg tables:
  - gold_hourly_prices
  - gold_hourly_movers

Validate:
```sql
select * from gold_hourly_prices order by hour_ts_sa desc limit 20;
```

## Job Schedule


### Collector job (every 5 minutes)
- Runs: [01_collector_write_raw](notebooks/01_collector_write_raw.ipynb)
- Goal: continuously land raw price snapshots

### Pipeline job (every 15 minutes or hourly)
Runs tasks in order:
1) [02_bronze_autoloader](notebooks/02_bronze_autoloader.ipynb)
2) [03_silver_transform](notebooks/03_silver_transform.ipynb)
3) [04_gold_aggregations](notebooks/04_gold_aggregations.ipynb)

Notes:
- `gold_hourly_movers` requires at least two hourly buckets to show % change
- Within the same hour, `samples` increases as more pulls arrive (proof of updates)


## About
Built by **Gift Mapote**: Data Engineer
- LinkedIn: www.linkedin.com/in/gift-mapote-6193801b0
- Email: giftmappte@gmail.com
