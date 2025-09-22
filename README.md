# [Project 1: End-to-End Azure Databricks Lakehouse — Traffic & Roads Analytics](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/01_End-to-End%20Azure%20Databricks%20Lakehouse%20%E2%80%93%20Traffic%20%26%20Roads%20Analytics)

## Overview
> Built an end-to-end Azure Databricks lakehouse on ADLS with Landing→Bronze→Silver→Gold Delta tables, using incremental ingestion and new-record-only transforms → Result: single source of truth \&\ Gold ready for reporting/data science.
> Established governance \&\ delivery with Unity Catalog + Power BI Service reporting + Azure DevOps CI/CD → Result: governed access \&\ repeatable releases with stakeholder insights.

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

# [Project 2: Microsoft Fabric LMS Lakehouse – Incremental Medallion Pipeline](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/02_Microsoft%20Fabric%20LMS%20Lakehouse%20%E2%80%93%20Incremental%20Medallion%20Pipeline)

## Overview
> Implemented an incremental Medallion lakehouse in Microsoft Fabric for daily LMS data: ADLS Gen2 Landing → Bronze ingestion, PySpark cleaning/business logic in Silver, Gold facts/dimensions + semantic model published to Power BI Service → Result: analytics-ready data powering insights on enrollments, progress, and completion.
> Automated and productionized with Fabric Data Factory and Azure DevOps (Git): orchestrated end-to-end pipelines and promoted Dev → Prod via CI/CD → Result: data-driven, repeatable releases and a fully automated end-to-end workflow.

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


# Project 3: Housing Finance Market Analytics (GCP BigQuery, SQL, Power Query, DAX)

## Summary
> End-to-end analytics project using Google BigQuery as the data source for a Power BI report with 3 pages: Market Overview, Sales Performance, and House Type Analysis.

## Tech Stack
Google Cloud Platform, BigQuery (SQL), Power BI Desktop & Service, Power Query, DAX

## Report Pages
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

## Modeling & Transformations
* BigQuery SQL: staging, dedupes, type casting, date logic; partition/cluster [column].
* Power Query: renaming, data types, merges, derived columns, filtering.
* Star Schema (recommended): FactSales + dimensions (DimDate, DimRegion, DimHouseType, DimAgent).

## Screenshot
