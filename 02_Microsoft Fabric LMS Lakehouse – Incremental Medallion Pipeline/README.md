# Microsoft Fabric LMS Lakehouse – Incremental Medallion Pipeline

## 1) Project Overview
An end‑to‑end data solution built on **Microsoft Fabric** using the **Medallion (Bronze → Silver → Gold)** architecture for an LMS (online learning platform) dataset. The pipeline ingests daily student/course/enrollment/assessment data, enforces quality in Silver, models Facts & Dimensions in Gold, and serves insights to Power BI.

![Architecture](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/23e1c5b803cee4871355c62ae665d6abd5028a40/images/Fabric_project_architecture.drawio.png)

View the Live Dashboard: https://app.powerbi.com/reportEmbed?reportId=3fd41b9f-72c4-4eb4-8f87-81ce787aa1ce&autoAuth=true&ctid=216e5950-5a9c-4dc3-96cf-437406f9c7a3
![](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/a90067ca8f3878a28984812bc858265eda57aad1/images/Fabric%20LMS%20Report.png)

## 2) Objectives
- Simulate incremental LMS data generation and land it in ADLS Gen2.
- Ingest to Fabric **Lakehouse** Bronze tables (compressed Delta).
- Apply **data cleaning** + **business transformations** in Silver.
- Model **Facts/Dimensions** in Gold and publish a **Power BI semantic model**.
- Orchestrate end‑to‑end with **Fabric Data Factory pipelines**.
- Keep the design governed and discoverable through **Unity Catalog**.

## 3) Scope of Work
- **Landing Zone**: Daily raw files stored in ADLS Gen2 under a `/landing` container.
- **Bronze Layer (Lakehouse)**: Incremental ingestion from Landing to Delta tables (raw parity, optimized storage).
- **Silver Layer (Lakehouse)**: Row‑level quality (duplicate removal, null handling), type standardization and business logic (e.g., completion metrics, performance scores).
- **Gold Layer (Lakehouse)**: Star‑schema modeling (Facts: learning activity, outcomes; Dimensions: Student, Course, Time, etc.).
- **Semantic Model**: Power BI dataset over Gold with relationships/measures ready for reporting.
- **Orchestration**: Data Factory pipelines for sequencing notebooks and table writes.
- **Governance**: Unity Catalog for lineage, permissions and auditing.

## 4) Data Flow
1. **Generate & Land**: Simulate daily LMS files → ADLS Gen2 `/landing`.
2. **Bronze**: Notebooks ingest incrementally (by process date/partition) into Delta tables.
3. **Silver**: Notebooks filter only new/changed rows and apply:
   - Duplicate & null handling
   - Type/format standardization (e.g., dates)
   - Logical consistency checks (e.g., completion date ≥ enrollment date)
   - Business transformations (e.g., *CompletionDays*, *PerformanceScore*, *OnTime/Delayed* flags)
4. **Gold**: Build **Fact/Dim** tables and create a semantic model for Power BI.
5. **Consumption**: Power BI reports, and optional Data Science/Analysis directly on the Gold Lakehouse.

## 5) Key Components
- **Microsoft Fabric**: Lakehouses, Notebooks (PySpark), Data Factory pipelines, OneLake.
- **ADLS Gen2**: Landing zone for raw files.
- **Delta Lake**: ACID tables for Bronze/Silver/Gold.
- **Unity Catalog**: Governance across layers.
- **Power BI**: Semantic model & reporting.

## 6) Incremental Strategy
- Partitioning/flags (e.g., `processing_date`) ensure each run processes **only newly landed rows**.
- Upsert/merge logic avoids reprocessing entire Bronze; Silver/Gold merges update existing keys and insert new ones.

## 7) Deliverables
- Fabric workspace with Lakehouses for **Bronze/Silver/Gold**.
- Reusable **Notebooks** for ingestion, cleaning, and modeling.
- **Data Factory Pipeline** orchestrating the end‑to‑end flow.
- **Power BI semantic model** for reporting (Fact/Dim relationships + core measures).
- Documentation (this README) and runbook.

## 8) Runbook
1. Drop the day’s files into ADLS Gen2 `/landing` (simulated generation).
2. Run **Bronze Ingestion** notebook/pipeline.
3. Run **Silver Transform** notebook (quality + business logic for today’s rows).
4. Run **Gold Modeling** notebook (Fact/Dim merges).
5. Refresh **Power BI** semantic model/report.

## 9) Environments
Parameterize workspace, paths and dates to reuse the same notebooks in **Dev / UAT / Prod**. Promote via Fabric deployment pipelines.

> This project was built based on what I learned from the course "Master Microsoft Fabric: A Complete End-to-End Project- CICD" by Shanmukh Sattiraju. I made some modifications and added new features to fit my own learning goals.
