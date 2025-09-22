# Power BI × Amazon Athena (via ODBC & Native Connector)

A hands-on Power BI project that connects to **Amazon Athena** using both the **Simba ODBC driver** and the **native Athena connector**, models & cleans data in **Power Query**, and publishes a polished **four-page** report with advanced UX (bookmarks, slicers, small multiples) and custom visuals (Radar chart from AppSource).

---

## Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
- [Power BI Desktop](#power-bi-desktop)
- [Data Cleaning Highlights](#data-cleaning-highlights)
- [Report Pages](#report-pages)
- [UX & Visuals](#ux--visuals)
- [Publishing](#publishing)
- [Troubleshooting](#troubleshooting)
- [Deliverables](#deliverables)
- [Folder Structure](#folder-structure)
- [Credits & Assets](#credits--assets)

---

## Overview
This project demonstrates end-to-end reporting against Athena-backed datasets:
- Stand up **AWS Glue** and **Athena** (catalogs, workgroups, S3 output/staging).
- Configure **IAM** for secure connectivity.
- Connect Power BI via **(1) ODBC (Simba)** and **(2) Amazon Athena connector**.
- Perform non-trivial **Power Query** cleaning (including a tricky **Year** column).
- Build a **four-page** report using bookmarks, slicers, small multiples, and a **Radar chart** (AppSource).
- Publish to **Power BI Service**.

---

## Architecture
```
S3 Data Lake ──► AWS Glue Data Catalog ──► Amazon Athena
                                         │
                                         ├─► ODBC (Simba) DSN ─► Power BI Desktop
                                         └─► Power BI's Amazon Athena connector
```
- **Athena Workgroup** with S3 query result location.
- **Glue** holds table metadata; Athena queries it.
- **Power BI** imports data, transforms in Power Query, and models visuals/DAX.

---

## Prerequisites
- **AWS account** with:
  - S3 bucket (data + `athena-results/`).
  - **AWS Glue** database/tables.
  - **Athena** workgroup with S3 output.
  - **IAM user/role** with Glue/Athena/S3 permissions (no hard-coded keys in PBIX).
- **Windows** machine with:
  - **Power BI Desktop (latest)**.
  - **Simba Amazon Athena ODBC Driver** installed.
  - Outbound access to AWS endpoints.
- Optional: corporate proxy details (if applicable).

---

## Setup

### 1) AWS Glue & Athena
1. Create a **Glue Database** and **Tables** (Crawler or manual DDL).
2. Create/configure **Athena Workgroup** with S3 **Query Result Location**.
3. Validate sample query in Athena console.

### 2) IAM
- Create an **IAM policy** allowing `athena:*`, `glue:*` (read), and `s3:GetObject/PutObject` on the staging/results paths.
- Attach to a user/role. Generate **programmatic credentials** if using ODBC with keys.

### 3) ODBC (Simba) DSN
- Install Simba Athena ODBC.
- Create a **System DSN**:
  - **Region**: e.g., `us-east-1`
  - **S3 Output/Staging**: `s3://<bucket>/athena-results/`
  - **Workgroup**: your workgroup
  - **Authentication**: IAM credentials or profile
- Test the DSN.

---

## Power BI Desktop

### Connect Options
**Option A — Native “Amazon Athena” connector**
- Get Data → **Amazon Athena** → enter region, SSO/AAD/IAM as applicable → pick database/tables.

**Option B — ODBC (Simba)**
- Get Data → **ODBC** → select your **System DSN** → Navigator → choose tables/views.

> Use **Import** for this project. If models grow, consider **DirectQuery** constraints and performance.

---

## Data Cleaning Highlights
- **Power Query** spent substantial effort on a problematic **Year** column (multiple formats, text/number inconsistencies).
- General steps used:
  - Normalize text `“NA”/“N/A”` → `null`.
  - **Type enforcement** (Dates, Numbers).
  - **Unpivot** to reshape wide tables for slicer-driven pages.
  - Conditional logic to standardize **Year** (e.g., 2-digit → 4-digit, strip non-numeric, cast).

**Example M snippet (Year normalization skeleton)**
```m
let
  Source = ...,
  ToNull = Table.TransformColumns(Source, {{"Year", each if _ = "NA" or _ = "N/A" then null else _, type text}}),
  CleanText = Table.TransformColumns(ToNull, {{"Year", each Text.Select(_, {"0".."9"}), type text}}),
  ToNumber = Table.TransformColumnTypes(CleanText, {{"Year", Int64.Type}}),
  Fix2Digit = Table.TransformColumns(ToNumber, {{"Year", each if _ <> null and _ < 100 then 2000 + _ else _, Int64.Type}})
in
  Fix2Digit
```

---

## Report Pages
1. **Page 1** — Overview (uses **Bookmarks + Bookmark Navigator**)  
   - Multiple states/views toggled via bookmarks to declutter the canvas.
2. **Page 2** — Slicer-driven Analysis  
   - Built on a table **recreated via Unpivot** to support flexible slicing.
3. **Page 3** — Comparative Insights  
   - Incorporates **Radar Chart** (AppSource) for multi-metric comparison.
4. **Page 4** — **Small Multiples Line Chart**  
   - Side-by-side trend lines for quick cohort pattern recognition.

---

## UX & Visuals
- **Bookmarks & Navigators** to switch views instead of overloading a single page.
- **Filters pane & card** styling for a consistent, branded look.
- **Custom visual**: **Radar Chart** from AppSource.
- Background images (provided) for professional presentation.

---

## Publishing
1. **Model checks** → refresh locally.
2. **Publish** to **Power BI Service** workspace.
3. Configure **dataset credentials** (Athena/ODBC) and schedule refresh if applicable.
4. Optional: package as a **Power BI App** for distribution.

---

## Troubleshooting
- **Connection errors** (ODBC): confirm **region**, **S3 staging path**, **workgroup**, and **IAM** rights; test DSN outside PBI.
- **Athena timeouts**: optimize queries, ensure Glue metadata correctness.
- **Year column loads as text**: apply Power Query steps before load; verify culture/locale.
- **Unpivot performance**: reduce columns, stage transformations in Dataflows if needed.

---

## Deliverables
- `*.pbix` — Power BI report (4 pages, cleaned model).
- `power-query/` — M scripts for Year normalization & Unpivot.
- `assets/` — Background images used by the report.
- `docs/` — Connection guide (Athena native vs. ODBC), IAM policy sample, refresh notes.

---

## Folder Structure
```
/README.md
/report/Project-Athena-ODBC.pbix
/power-query/YearNormalization.m
/power-query/UnpivotExample.m
/assets/backgrounds/*.png
/docs/connection-guide.md
/docs/iam-policy-sample.json
```

---

## Credits & Assets
- **Custom visual**: Radar Chart from **Microsoft AppSource**.
- **Background images**: Provided with the course/resources; ensure license compliance for production.

---

> Tip: Keep secrets out of PBIX by using **Parameter** queries and **Service credentials**. For teams, store DSN specs and IAM policy JSONs in `/docs` with environment placeholders.

