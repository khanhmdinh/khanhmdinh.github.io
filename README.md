# Project 1: Online LMS System (Microsoft Fabric, Spark/SQL, Delta Lake, Power BI)

## Summary
> Production-style data Lakehouse in Microsoft Fabric implementing the Medallion architecture (Bronze → Silver → Gold). The project covers ingestion, transformation, data quality, orchestration, and serving curated insights to the reporting layer via Power BI (Direct Lake).

## Objectives
* Build a scalable Lakehouse on OneLake using Fabric components.
* Orchestrate ingestion → transformation → serving with robust data-quality checks at each layer.
* Expose a governed semantic model to Power BI (Direct Lake) for low-latency analytics.

## Tech Stack
Microsoft Fabric: Data Factory (Pipelines), Lakehouse / OneLake, Notebooks (Spark/PySpark & SQL), Delta Lake, Power BI (Semantic Model, Direct Lake), Dataflows Gen2

## Architecture

*/
External Sources → Data Factory (Pipelines/Copy/Dataflows) → Lakehouse (Bronze Δ)
                                              ↓
                                        Notebooks (Spark/SQL)
                                              ↓
                                      Lakehouse (Silver Δ)
                                              ↓
                               SQL/Views + Business Rules (Gold Δ)
                                              ↓
                        Power BI Semantic Model (Direct Lake) → Reports/Apps
/*

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

# Project 2: Men's T-shirt Insigh BI Collection (Microsoft Azure SQL \&\ Power BI, DAX)

## Summary
> Short, end-to-end analytics project using Azure SQL Database as the source for Power BI dashboards. Includes cloud setup (provisioning + firewall), data cleaning in SQL, DAX modeling, custom AppSource visual (ticker), and governed sharing via Power BI Apps.

## Objectives
* Stand up a reliable cloud data source for BI consumption.
* Model men’s apparel data and surface brand/product performance.
* Publish a multi-page, widescreen Power BI report and distribute via an App.

## Tech Stack
Azure SQL Database, T-SQL, Power BI Desktop & Service, DAX, AppSource Custom Visuals

## Highlights
* Provisioned Azure SQL DB, configured firewall and authentication; connected from Power BI via: Database connector & Microsoft Account sign-in.
* Performed data cleaning & transformations in T-SQL; created DAX calculated columns for derived metrics.
* Designed multi-page, widescreen layout (brand overview, product performance); added AppSource ticker for key updates.
* Published to Power BI Service and packaged as a Power BI App for governed sharing.

## Architecture
*/
Azure SQL Database  →  Power BI Desktop (Import/DirectQuery)  →  Power BI Service  →  Power BI App (distribution)
         ▲                                │
  Firewall & Auth                          └─ DAX modeling + visuals (incl. AppSource ticker)
/*

## Data (domain)
* Men’s apparel dataset with brand and product attributes plus sales/transactions.
* Replace with specifics once finalized: ~[N tables], ~[M rows], date range [YYYY-MM]…

## Setup
1. Azure SQL
* Create DB, enable client IP in Firewall.
* Load source tables (BCP/Azure Data Studio/SQL scripts in /sql).

2. Power BI Desktop
* Get Data → Azure SQL Database → enter server/db; choose Import (default) or DirectQuery.
* Add DAX calculated columns/measures as needed.
* Install AppSource ticker (custom visual) and place on header band.
* Optimize page size for widescreen.

3. Publish & Share
* Publish to Power BI Service → configure refresh (if Import).
* Create a Power BI App, add audiences, and share with stakeholders.

## Screenshots / Demo
## Status & Next Steps
* MVP complete: cloud source, model, visuals, App distribution.
* Add KPI drillthroughs, RLS (if needed), performance tuning, and automated CI/CD for PBIX.

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
*/
Data Source(s) → BigQuery (staging + models/views) → Power BI Desktop (Import/DirectQuery)
                                        │
                                        └─ Power Query (cleaning) + DAX (measures)
→ Power BI Service (dataset + report) → App / Audiences (governed sharing)
/*

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

* Screenshot (to be updating)
