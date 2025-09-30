# Data Analytics & Power BI Portfolio — 6 End‑to‑End Projects

A fast overview of my recent hands‑on projects across Microsoft Fabric, Azure Databricks, AWS Athena, Google BigQuery, and Power BI.

> **What you’ll see:** medallion architectures, incremental ingestion, data quality & transformations, semantic modeling, and production‑ready reports.

---

## 🔎 At‑a‑glance

| Project | Domain | Stack | What I built | Links |
|---|---|---|---|---|
| **[End-to-End Azure Databricks Lakehouse — Traffic & Roads Analytics](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/01_End-to-End%20Azure%20Databricks%20Lakehouse%20%E2%80%93%20Traffic%20%26%20Roads%20Analytics)** | Transport | Azure Databricks (Auto Loader, Structured Streaming), ADLS Gen2, Unity Catalog, Delta/Medallion, Power BI | Auto Loader to Bronze, streaming DQ & transforms to Silver (EV & Motor counts, road categories), Gold tables, Jobs + File‑arrival & Schedule triggers. | [Overview](pdfs/pdfs/End-to-End_Azure_Databricks_Lakehouse__Traffic__Roads_Analytics.pdf) |
| **[Microsoft Fabric LMS Lakehouse – Incremental Medallion Pipeline](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/02_Microsoft%20Fabric%20LMS%20Lakehouse%20%E2%80%93%20Incremental%20Medallion%20Pipeline)** | EdTech | Microsoft Fabric (Lakehouse, Notebooks, Data Factory), Delta/Medallion, Power BI | Ingestion → Bronze/Silver/Gold with DQ rules (null/dup/date checks), business transforms (completion KPIs), MERGE upserts, Fact/Dim model + Semantic model. | [Overview](pdfs/Microsoft_Fabric_LMS_Lakehouse__Incremental_Medallion_Pipeline.pdf) |
| **[Housing Finance Market Analytics (GCP BigQuery, SQL, Power Query, DAX)](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/03_Housing%20Finance%20Market%20Analytics)** | Real Estate | Google BigQuery (SQL), Power Query, Power BI, DAX | Three‑page report (Overview, Sales, House Types), SQL+PQ cleaning, >10 DAX measures (YoY, CALCULATE/FILTER), publish to Service. | [Overview](pdfs/Housing_Finance_Market_Analytics.pdf) · [Case Study](pdfs/Housing_Finance_Market_Analytics_Project_Showcase.pdf) |
| **[Loan Default Risk Dashboard](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/04_Loan%20Default%20Project)** | Financial Services | Power BI Dataflows, Power Query, DAX, Incremental Refresh | 3‑page dashboard (Overview, Demographics, Risk), profiling/cleaning/validation, DAX measures, scheduled + incremental refresh, share via Apps. | [Overview](pdfs/Loan_Default_Risk_Analytics.pdf) · [Case Study](pdfs/Loan_Default_Risk_Analytics_Project_Showcase.pdf) |
| **[Video Games Sales Analytics](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/05_Video%20Games%20Project)** | Retail / Gaming | AWS Glue & Athena, ODBC (Simba), Power BI, Bookmarks | End‑to‑end AWS setup (IAM, Glue catalog, Athena), robust data cleaning, custom visuals (Radar), bookmarks & nav, 4 report pages. | [Overview](pdfs/Video_Games_Sales_Analytics.pdf) · [Case Study](pdfs/Video_Games_Sales_Analytics_Project_Showcase.pdf) |
| **[Menswear Insights Hub](https://github.com/khanhmdinh/khanhmdinh.github.io/tree/main/06_Menswear%20Insights%20Hub)** | Retail / e‑Com | Azure SQL DB, Power BI, DAX, Custom Visuals | Azure SQL setup + firewall, data cleaning in SQL, connection via Database & MS Account, DAX calculated columns, AppSource scroller visual, app sharing. | [Overview](pdfs/Menswear_Insights_Hub.pdf) · [Case Study](pdfs/Menswear_Insights_Hub_Project_Showcase.pdf) |

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

- **Data Platforms:** Microsoft Fabric, Azure Databricks, Google BigQuery, AWS Athena/Glue, Azure SQL, ADLS Gen2, Unity Catalog 
- **Processing:** PySpark, Delta Lake, Structured Streaming, Fabric Notebooks  
- **Orchestration:** Fabric Data Factory, Databricks Jobs (triggers), Parameterized pipelines  
- **BI:** Power BI Desktop/Service, Dataflows, DAX (CALCULATE, FILTER, TIME‑INTEL, etc.), Bookmarks & Slicers, Custom visuals  
- **Dev Practices:** Environment promotion (dev/uat/prod), modular notebooks, README‑first documentation

---

## 🚀 How to use this site

1. Scan the **At‑a‑glance** table above.
2. Read the **Overview** to see scope, architecture, pipeline design decisions, and results.
3. Open any **Case Study** to follow the story behind each charts and insights.
5. (Optional) Dive into notebooks/SQL and infra diagrams.

---

> Happy to walk you through the projects live, discuss trade‑offs, or dive into the code!
