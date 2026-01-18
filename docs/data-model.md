# Data Model

This project follows a Medallion (Bronze -> Silver -> Gold). 
Bronze keeps raw-ish data, Silver is clean and consistent, and Gold is optimized for dashboards.

## Timezone rule
- Store event time in **UTC** (for consistency)
- Also provide **South Africa time** columns (*_sa) for reporting and bucketing

-----
## Bronze

### `bronze_prices_raw`
**Purpose:** Raw ingestion from ADLS using Auto Loader.

**Key columns:**
- `ingest_ts` - when Databricks ingested the file
- `source_file` - raw file path in ADLS
- Raw payload fields like `source`, `vs_currency`, `pulled_at_utc`, `pulled_at_sa`, `data` (nested)

**Used for:** lineage/debugging, replaying ingestion if needed/

-----
## Silver
### `silver_prices`
**Purpose:** Clean, normalized table for analytics.
**Key columns:**
- `asset_id` - e.g. bitcoin, ripple
- `vs_currency` - zar
- `price` - numeric price
- 'event_ts` - (UTC) - from API 'last_updated_at` (fallback: pull time)
- event_ts_sa - `event_ts` converted to Africa/Johannesburg

**Used for:** building Gold aggregates and validation queries.

----
## Gold
### `gold_hourly_prices`
**Purpose:** Hourly trend table in South African time.
**Key columns:**
- `hour_ts_sa` - SA hour bucket
- `avg_price`, `min_price`, `max_price`

**Used for:** trend line chart.

### `gold_hourly_movers
**Purpose:** Hour-over-hour % change table (SA time)
**Key columns:**
- `hour_ts_sa`
- `avg_price`, `prev_avg_price`
- `pct_change` - % change vs previous hour

**Used for:** "Top movers" table.

----
## Dashboard fields
- Trend line:
  - X = `hour_ts_sa`
  - Y = `avg_price`
  - Series/Color = `asset_id`
  - Tooltip = `sample`
- Movers table:
  - `asset_id`, `pct_change`, `avg_price`, `prev_avg_price`, `hour_ts_sa`

----
