# End-to-End Azure Databricks Lakehouse – Traffic & Roads Analytics

## 1) Project Overview
A Databricks implementation of the **Medallion architecture** (Bronze → Silver → Gold) for UK **traffic counts** and **roads** datasets. This README covers the **foundational setup**—ADLS Gen2 containers, **Unity Catalog** objects, and Delta tables—so the pipeline can ingest from a landing zone to Bronze and transform onward to Silver/Gold.

![](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/fa5291dbc0d0b5317adc7a09dd55172104d01cd5/images/Azure_Databricks_project-architecture.drawio.png)

## 2) Expected Setup
- **ADLS Gen2** containers (e.g., `landing`, `checkpoint`, `bronze`, `silver`, `gold`).
- **Unity Catalog** governance with a single Catalog (e.g., `dev_catalog`) and Schemas:
  - `bronze` (raw parity tables written by Autoloader/Structured Streaming)
  - `silver` (cleaned & conformed tables)
  - `gold` (star‑schema, reporting‑ready)
- **External Locations** mapped to ADLS paths and referenced in UC.
- **Delta tables** in each schema.

## 3) Data Flow
1. **Landing**: Raw files manually uploaded (course focus is Databricks, not ADF). In real projects, ingest with ADF or similar.
2. **Bronze**: Incremental load to Delta tables using **Autoloader** (`cloudFiles`) with checkpoints and schema locations.
3. **Silver**: Read from Bronze via **Structured Streaming**; apply **incremental transformations** (drop duplicates, handle nulls, standardize types) and dataset‑specific logic.
4. **Gold**: Minimal transformations into reporting tables used by analytics/Power BI.
5. **Consumption**: Analytics, data science, and Power BI read Gold. All is **governed by Unity Catalog**.

## 4) Best‑Practice PySpark Patterns
- **Parameterize** workspace/env, catalog, and paths (avoid hard‑coding).
- Use **Autoloader** options: `schemaLocation`, `checkpointLocation`, `cloudFiles.format`, `header`, **explicit schemas**.
- Maintain **merge/upsert** logic to avoid full-table rewrites.
- Stream reads from Bronze Delta to achieve **incremental Silver**.
- Centralize **common utilities** (e.g., null handling, duplicate removal) for reuse across notebooks and environments (Dev/UAT/Prod).

## 5) Deliverables
- Infrastructure scaffolding (containers, external locations).
- Unity Catalog objects (catalog, schemas, grants).
- Bronze tables ready for incremental ingestion.
- Documentation (this README) for environment‑agnostic reuse.
- All layers are backed by ADLS Gen2 and governed by **Unity Catalog**.

>This project was built based on what I learned from the course "Azure Databricks end to end project with Unity Catalog CICD" by Shanmukh Sattiraju. I made some modifications and added new features to fit my own learning goals.
