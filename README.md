# databricks-price-movers

##   Databricks Price Movers (ZAR) - Real-time-ish Crypto Trends in South African Time

## Purpose
Build a production-style data engineering pipeline on **Azure Databricks** that ingests live crypto price data, processes it through Medallion lakehouse design.
It will serve an interactive dashboard for tracking **what us moving now** and how **prices trend over time** - using **South African time**.


## Objective
Most "portolio projects" stop at a notebook and don't prove real data engineering skills like:
- ingesting data repeatedly on a schedule
- hanfling schema drift in semi-structured JSON
- building clean analytical tables (bronze -> silver -> gold)
- supporting both "current view" and "historical trends"
- making outputs usable by non-techinical users (dashboard)

This projec solves those gaps by delivering a complete pipeline that:
1) pulls prices every few minutes from a public API
2) lands raw files in data lake storage (ADLS)
3) transforms them into clean Delta tables
4) computes hourly SA-time aggregates and movers
5) serves a dashboard that can be refreshed and used daily

## What you can do with the dashboars
- See **hourly trend** per assest (in SA time)
- See **top movers** for the latest hour
- Filter by assest (e.g., Ripple) to inspec trend


## Example use case
A trader, analyst, or fintech team can use this dashboard to quickly identify which assets are gaining/losing momentum and review recent movement history without manually checking multiple exchanges or websites.

