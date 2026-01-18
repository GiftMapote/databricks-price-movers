# databricks-price-movers

## Databricks Price Movers (ZAR) — Real-time-ish Crypto Trends in South African Time

## Purpose
Build a production-style data engineering pipeline on **Azure Databricks** that ingests crypto price data, processes it using a **Medallion (Bronze → Silver → Gold) Lakehouse** design, and serves an interactive dashboard.

The dashboard helps you track:
- what is moving **right now**
- how prices **trend over time**
- all displayed in **South African time (Africa/Johannesburg)**
- 

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
