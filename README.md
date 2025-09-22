# Data Analytics & Power BI Portfolio — 6 End‑to‑End Projects

A fast, recruiter‑friendly overview of my recent hands‑on projects across Microsoft Fabric, Azure Databricks, AWS Athena, Google BigQuery, and Power BI. Each case study links to a deeper README and artifacts (PBIX/PDF).

> **What you’ll see:** real medallion architectures, incremental ingestion, data quality & transformations, semantic modeling, and production‑ready reports.

---

## 🔎 At‑a‑glance

| Project | Domain | Stack | What I built | Links |
|---|---|---|---|---|
| **[End-to-End Azure Databricks Lakehouse — Traffic & Roads Analytics](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/01_End-to-End%20Azure%20Databricks%20Lakehouse%20%E2%80%93%20Traffic%20%26%20Roads%20Analytics)** | Transport | Azure Databricks (Auto Loader, Structured Streaming), ADLS Gen2, Unity Catalog, Delta/Medallion, Power BI | Auto Loader to Bronze, streaming DQ & transforms to Silver (EV & Motor counts, road categories), Gold tables, Jobs + File‑arrival & Schedule triggers. | [Case Study](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/main/01_End-to-End%20Azure%20Databricks%20Lakehouse%20%E2%80%93%20Traffic%20%26%20Roads%20Analytics/README.md) · [PDF](docs/pdfs/Databricks_Traffic_Roads.pdf) |
| **[Microsoft Fabric LMS Lakehouse – Incremental Medallion Pipeline](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/02_Microsoft%20Fabric%20LMS%20Lakehouse%20%E2%80%93%20Incremental%20Medallion%20Pipeline)** | EdTech | Microsoft Fabric (Lakehouse, Notebooks, Data Factory), Delta/Medallion, Power BI | Ingestion → Bronze/Silver/Gold with DQ rules (null/dup/date checks), business transforms (completion KPIs), MERGE upserts, Fact/Dim model + Semantic model. | [Case Study](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/main/02_Microsoft%20Fabric%20LMS%20Lakehouse%20%E2%80%93%20Incremental%20Medallion%20Pipeline/README.md) · [PDF](docs/pdfs/Fabric_LMS.pdf) |
| **[Housing Finance Market Analytics (GCP BigQuery, SQL, Power Query, DAX)](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/03_Housing%20Finance%20Market%20Analytics)** | Real Estate | Google BigQuery (SQL), Power Query, Power BI, DAX | Three‑page report (Overview, Sales, House Types), SQL+PQ cleaning, >10 DAX measures (YoY, CALCULATE/FILTER), publish to Service. | [Case Study]([projects/housing-bigquery/README.md](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/main/03_Housing%20Finance%20Market%20Analytics/README.md)) · [Slides](docs/pdfs/Housing.pdf) |
| **[Loan Default Risk Dashboard](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/04_Loan%20Default%20Project)** | Financial Services | Power BI Dataflows, Power Query, DAX, Incremental Refresh | 3‑page dashboard (Overview, Demographics, Risk), profiling/cleaning/validation, DAX measures, scheduled + incremental refresh, share via Apps. | [Case Study](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/main/04_Loan%20Default%20Project/README.md) · [Slides](docs/pdfs/Loan_Default.pdf) |
| **[Video Games Sales Analytics](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/05_Video%20Games%20Project)** | Retail / Gaming | AWS Glue & Athena, ODBC (Simba), Power BI, Bookmarks | End‑to‑end AWS setup (IAM, Glue catalog, Athena), robust data cleaning, custom visuals (Radar), bookmarks & nav, 4 report pages. | [Case Study](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/main/05_Video%20Games%20Project/README.md) · [Slides](docs/pdfs/Video_Games_Athena.pdf) |
| **[Menswear Insights Hub](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/06_Menswear%20Insights%20Hub)** | Retail / e‑Com | Azure SQL DB, Power BI, DAX, Custom Visuals | Azure SQL setup + firewall, data cleaning in SQL, connection via Database & MS Account, DAX calculated columns, AppSource scroller visual, app sharing. | [Case Study]([projects/menswear-azure-sql/README.md](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/main/06_Menswear%20Insights%20Hub/README.md)) · [Slides](docs/pdfs/Menswear.pdf) |

---

## 🧱 Architecture patterns I apply

- **Medallion Techniques (Bronze → Silver → Gold)** with Delta Lake for governance & performance.
- **Incremental Ingestion** (Databricks Auto Loader / Structured Streaming; Fabric Pipelines).
- **Data Quality Gates** (dup/null handling, schema & date standardization, logical checks).
- **Business Transforms** to analytics‑ready features (e.g., course completion KPIs, EV & Motor counts, road categorization, model scores).
- **Upserts (MERGE)** to keep Silver/Gold tables current.
- **Semantic Modeling**: Fact/Dim, relationships, and measures for Power BI.
- **Automation**: Databricks Jobs (file‑arrival & schedules), Fabric Data Factory pipelines.
- **Deployment Ready**: parameterized notebooks, environment‑aware paths (dev/uat/prod).

---

## 🛠️ Skills & Tools

**Data Platforms:** Microsoft Fabric, Azure Databricks, Google BigQuery, AWS Athena/Glue, Azure SQL, ADLS Gen2, Unity Catalog 
**Processing:** PySpark, Delta Lake, Structured Streaming, Fabric Notebooks  
**Orchestration:** Fabric Data Factory, Databricks Jobs (triggers), Parameterized pipelines  
**BI:** Power BI Desktop/Service, Dataflows, DAX (CALCULATE, FILTER, TIME‑INTEL, etc.), Bookmarks & Slicers, Custom visuals  
**Dev Practices:** Environment promotion (dev/uat/prod), modular notebooks, README‑first documentation

---

## 🚀 How to use this site

1. Scan the **At‑a‑glance** table above.  
2. Open any **Case Study** to see scope, design decisions, code snippets, and results.  
3. Check the **PDF** to preview the final dashboards or architecture quickly.  

> Happy to walk you through the projects live, discuss trade‑offs, or dive into the code!
