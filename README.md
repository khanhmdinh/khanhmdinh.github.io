# Project 1: End-to-End Azure Databricks Lakehouse — Traffic & Roads Analytics

## Overview
> Built an end-to-end Azure Databricks lakehouse on ADLS with Landing→Bronze→Silver→Gold Delta tables, using incremental ingestion and new-record-only transforms → Result: single source of truth \&\ Gold ready for reporting/data science.
> Established governance \&\ delivery with Unity Catalog + Power BI Service reporting + Azure DevOps CI/CD → Result: governed access \&\ repeatable releases with stakeholder insights.

## Objectives
Convert Traffic and Roads datasets into reliable, analytics-ready Gold tables for reporting and data science, using Azure Databricks with the Medallion architecture and Delta Lake. Govern data via Unity Catalog and publish insights to Power BI Service. Integrate CI/CD with Azure DevOps.

## Tech Stack
* Databricks (PySpark, Structured Streaming/Auto Loader), Delta Lake (Bronze/Silver/Gold), ADLS Gen2, Unity Catalog, Power BI, Azure DevOps

## Architecture
```postgresql
ADLS Gen2 (Landing: traffic, roads)
        │  [incremental copy]
        ▼
Bronze (Delta)  --(Unity Catalog governs)--
        │  [transform only new records via notebooks]
        ▼
Silver (Delta)  --(Unity Catalog governs)--
        │  [minimal shaping for analytics]
        ▼
Gold (Delta)    --(Unity Catalog governs)--> Power BI Service (reports)

Databricks Notebooks (PySpark, incremental in batch mode) drive Landing→Bronze→Silver→Gold
Azure DevOps (Git) provides CI/CD (Dev → Prod) for notebooks/jobs/permissions

```

## Data Flow
1. Place raw traffic and roads files in ADLS Gen2 Landing (manual drop to emulate ETL).
2. Run Databricks notebook to incrementally load Landing → Bronze (Delta), appending only new files/records.
3. Apply transformations in Silver: data cleaning, business rules, process only new records (idempotent change-aware logic).
4. Create Gold tables with minimal shaping for analytics/reporting.
5. Build a Power BI report to surface insights (e.g., volume, congestion, hotspot patterns).
6. Control access and lineage through Unity Catalog.
7. Use Azure DevOps (Git) to promote notebooks/jobs/config from Dev → Prod (CI/CD).

# Project 2: Microsoft Fabric LMS Lakehouse – Incremental Medallion Pipeline

## Overview
> Implemented an incremental Medallion lakehouse in Microsoft Fabric for daily LMS data: ADLS Gen2 Landing → Bronze ingestion, PySpark cleaning/business logic in Silver, Gold facts/dimensions + semantic model published to Power BI Service → Result: analytics-ready data powering insights on enrollments, progress, and completion.
> Automated and productionized with Fabric Data Factory and Azure DevOps (Git): orchestrated end-to-end pipelines and promoted Dev → Prod via CI/CD → Result: data-driven, repeatable releases and a fully automated end-to-end workflow.

## Objectives
* Build a scalable Lakehouse on OneLake using Fabric components.
* Orchestrate ingestion → transformation → serving with robust data-quality checks at each layer.
* Expose a governed semantic model to Power BI (Direct Lake) for low-latency analytics.

## Tech Stack
Microsoft Fabric: Data Factory (Pipelines), Lakehouse / OneLake, Notebooks (Spark/PySpark & SQL), Delta Lake, Power BI (Semantic Model, Direct Lake), Dataflows Gen2

## Architecture

```
External Sources → Data Factory (Pipelines/Copy/Dataflows) → Lakehouse (Bronze Δ)
                                              ↓
                                        Notebooks (Spark/SQL)
                                              ↓
                                      Lakehouse (Silver Δ)
                                              ↓
                               SQL/Views + Business Rules (Gold Δ)
                                              ↓
                        Power BI Semantic Model (Direct Lake) → Reports/Apps
```

## Medallion Layers
* Bronze (Raw) – Landing zone (append-only). Schema-on-read; minimal checks.
* Silver (Refined) – Cleaned, conformed entities; deduped; types & keys enforced; SCD/UPSERTs.
* Gold (Curated) – Analytics-ready (facts & dimensions, business rules, KPI logic), exposed to BI.

## Data Domain & Scope

## Orchestration & Scheduling
* Pipelines (Fabric Data Factory)
1. Ingest_Bronze: copy data from sources/shortcuts into Bronze (incremental by watermark).
2. Transform_Silver: run Notebook activities (clean, dedupe, type cast, UPSERT).
3. Publish_Gold: apply SQL business rules; build views/tables for BI; invalidate/refresh semantic model if needed.

* Monitoring: activity run history, alerts on failure, retry policies.

## Data Quality & Governance
* Bronze → Silver: schema validation, null/dup checks, referential keys, business rule flags.
* Promotion gates: only pass records meeting rules; quarantine exceptions.
* Lineage: Fabric lineage view across pipelines, Lakehouse tables, and BI artifacts.
* Security: workspace roles; Power BI RLS at Gold (by region/business unit); sensitivity labels.

## Performance Practices
* Partition large Delta tables by [Date/Entity]; compact small files when needed.
* Incremental loads with watermark columns; avoid full scans.
* Model Gold as star schema (Fact/Dim) for BI.
* Prefer Direct Lake for low latency; reduce visuals per page and avoid overly complex DAX.

## Setup
1. Workspace & Lakehouse
* Create a Fabric Workspace with Lakehouse capacity.
* Create Lakehouse: lakehouse_[name]. Add Shortcuts to external data (if applicable).

2. Pipelines
* Build Ingest_Bronze (Copy activity/Dataflows Gen2) → write to /Tables/bronze/* (Delta).
* Build Transform_Silver (Notebook activities) → write to /Tables/silver/*.
* Build Publish_Gold (SQL & Notebook mix) → create curated /Tables/gold/* and SQL views.

3. Power BI (Direct Lake)
* Create Semantic model from Lakehouse Gold tables; set relationships, KPIs, and measures.
* Build reports; publish App; assign audiences & permissions.
* Configure refresh/schedules per SLA.

## Reporting Layer (Power BI)
* Pages: Executive Overview, Sales Performance, Operations/Inventory (example).
* Slicers: Date, Region, Product/Entity.
* Direct Lake semantic model for near-instant visuals.

# Project 3: Housing Finance Market Analytics (GCP BigQuery, SQL, Power Query, DAX)

## Summary
> End-to-end analytics project using Google BigQuery as the data source for a Power BI report with 3 pages: Market Overview, Sales Performance, and House Type Analysis.

## Objectives
* Stand up a reliable GCP BigQuery data layer for BI consumption.
* Model real-estate data to surface market, sales, and house-type insights.
* Publish a 3-page Power BI report with interview-ready DAX patterns.

## Tech Stack
Google Cloud Platform, BigQuery (SQL), Power BI Desktop & Service, Power Query, DAX

## Report Pages (Scope)
* Housing Market Overview — headline KPIs, trend lines, geo/region breakdown.
* Sales Performance — revenue/transactions, YoY/MTD/YTD, top regions/agents.
* House Type Analysis — product mix by property type, price bands, inventory.

## Architecture
```
Data Source(s) → BigQuery (staging + models/views) → Power BI Desktop (Import/DirectQuery)
                                        │
                                        └─ Power Query (cleaning) + DAX (measures)
→ Power BI Service (dataset + report) → App / Audiences (governed sharing)
```

## Data (Domain & Scale)
* Domain: Housing/real-estate transactions & listings.
* Scale: [~M rows], [N tables/views], date range [YYYY-MM to YYYY-MM].
* Entities: Listings, Sales, Regions, Agents, HouseTypes, Calendar.

## Modeling & Transformations
* BigQuery SQL: staging, dedupes, type casting, date logic; partition/cluster [column].
* Power Query: renaming, data types, merges, derived columns, filtering.
* Star Schema (recommended): FactSales + dimensions (DimDate, DimRegion, DimHouseType, DimAgent).

## DAX Measures
* Core: Total Sales, Units Sold, Average Price, GM% (optional).
* Time intelligence: YoY Growth, YTD, MTD, Rolling 12M.
* Segmentation: mix % by house type, contribution by region/agent.

## Setup
1. GCP BigQuery
* Create project & dataset [project_id].[dataset].
* Load source tables (CSV, external tables, or SQL in /sql).

2. Power BI Desktop
* Get Data → BigQuery → select project/dataset; choose Import (default) or DirectQuery.
* Apply Power Query steps (cleaning, merges).
* Add DAX measures; create a Date table and set Mark as Date Table.
* Build 3 pages with slicers (Date, Region, House Type).

3. Public
* Publish to Power BI Service.
* Configure refresh (no gateway needed for BigQuery).
* Package via Power BI App; define audiences & permissions.

## Screenshot (to be updating)
