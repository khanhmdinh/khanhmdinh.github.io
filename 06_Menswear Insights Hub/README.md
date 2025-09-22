# Power BI × Azure SQL — Menswear Insights Hub

A practical Power BI project that connects to **Azure SQL Database** (men’s clothing dataset), performs data understanding/cleaning, adds calculated columns with **DAX**, applies thoughtful **report design** (custom page size, branded layout, AppSource scroller), and publishes & shares via **Power BI Apps**.

---

## Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Setup: Azure & Data Source](#setup-azure--data-source)
- [Connect from Power BI Desktop](#connect-from-power-bi-desktop)
- [Data Understanding & Cleaning](#data-understanding--cleaning)
- [DAX Examples (Calculated Columns/Measures)](#dax-examples-calculated-columnsmeasures)
- [Report Design & UX](#report-design--ux)
- [Publishing & Sharing](#publishing--sharing)
- [Troubleshooting](#troubleshooting)
- [Deliverables](#deliverables)
- [Suggested Folder Structure](#suggested-folder-structure)
- [Notes](#notes)

---

## Overview
- **Data Source:** Azure SQL Database (men’s clothing data).
- **Objective:** Build a polished, multi‑page Power BI report highlighting brands, pricing, and sales signals.
- **What you’ll learn:**  
  - Setting up Azure SQL & firewall for secure access  
  - Connecting Power BI via both **Database** and **Microsoft Account (AAD)** methods  
  - Cleaning & validating data (in Azure SQL and in Power Query)  
  - Creating calculated columns with **DAX**  
  - Report page design (custom sizes, layout, AppSource scroller)  
  - **Sharing via Power BI Apps**

---

## Architecture
```
Azure SQL DB  ──►  Power BI Desktop  ──►  Power BI Service (Workspace)  ──►  App (Sharing)
        ▲
        └─ Azure Portal (DB provisioning, Firewall, SQL user/AAD setup)
```

---

## Prerequisites
- Azure subscription with permission to create **Azure SQL Database**.
- **Power BI Desktop (latest)** on Windows.
- Network access to Azure SQL public endpoint (or Private Link if used).  
- (Optional) Azure AD tenant access if using **Microsoft Account** (Azure AD) authentication.

---

## Setup: Azure & Data Source
1. **Provision Azure SQL Database**
   - Azure Portal → *Create* SQL Database (Basic/General Purpose is fine for demos).
   - Record **Server name**, **Database name**, and admin credentials.

2. **Configure Firewall**
   - In the SQL server resource → *Networking* → *Firewall and virtual networks* → **Add client IP** (or range).  
   - Alternatively, allow Azure services if appropriate.

3. **Load the Dataset**
   - Create schema & tables for the men’s clothing dataset.
   - Optionally stage and transform with T‑SQL views (e.g., clean brand names, normalize prices).

4. **Security**
   - Create least‑privilege login/user for Power BI (SQL Auth or AAD user).  
   - Avoid using server admin credentials in reports.

---

## Connect from Power BI Desktop
Two supported connection methods from the transcript:

### Option A — *Database* (SQL Authentication)
- **Get Data → Azure → Azure SQL Database**  
- Server: `yourservername.database.windows.net`  
- Database: `<YourDB>`  
- Authentication: **Database** (SQL username/password)

### Option B — *Microsoft Account* (Azure AD)
- **Get Data → Azure → Azure SQL Database**  
- Choose **Microsoft Account** and sign in with AAD credentials that have DB access.

> Use **Import** mode for this project. Configure scheduled refresh later in the Service.

---

## Data Understanding & Cleaning
Perform initial profiling (row counts, distincts, nulls) in **Azure SQL** and refine in **Power Query**:
- **Azure SQL (T‑SQL):** standardize brand names, fix inconsistent categories, validate numeric columns (prices, discounts), prepare views for clean input.
- **Power Query:**
  - Replace text placeholders (`'NA'`, `'N/A'`) with `null`.
  - Enforce types (Decimal for prices, Whole/Decimal for quantities & discounts, Date for order dates).
  - Trim/clean text; standardize casing if required.
  - Remove redundant intermediate columns after deriving final fields (to keep model lean).

---

## DAX Examples (Calculated Columns/Measures)
Add business logic with DAX to support visuals and KPIs (illustrative examples):

**Calculated Columns**
```DAX
Price Band =
VAR p = 'Sales'[Unit Price]
RETURN
    IF(p < 25, "Budget",
        IF(p < 75, "Mid-Range", "Premium")
    )

Net Price =
'Sales'[Unit Price] * (1 - 'Sales'[Discount %])
```

**Measures**
```DAX
Total Revenue := SUMX('Sales', 'Sales'[Quantity] * 'Sales'[Net Price])

Avg Selling Price :=
DIVIDE( [Total Revenue], SUM('Sales'[Quantity]) )

Gross Margin :=
VAR Revenue = [Total Revenue]
VAR COGS    = SUM('Sales'[Quantity] * 'Sales'[Unit Cost])
RETURN DIVIDE(Revenue - COGS, Revenue)

Brand Count := DISTINCTCOUNT('Products'[Brand])
```

> Tailor formulas to the final column names in your model.

---

## Report Design & UX
- **Custom Page Size:** Set a larger canvas for a dashboard‑style layout (*Format → Canvas → Type: Custom*).  
- **Brand Overview (Page 1):** Show brands prominently; include KPIs (Revenue, Margin, Units).  
- **Company Scroller (Page 2):** Add **Scroller** custom visual from **AppSource** for a marquee‑style header.  
- **Consistency:** Align cards/charts to a grid; apply brand colors & typography.  
- **Accessibility:** Sufficient contrast; alt text for key visuals; meaningful titles.

---

## Publishing & Sharing
1. **Publish** the PBIX to a Power BI **Workspace**.
2. In **Datasets → Settings**, configure **Credentials** (SQL or Microsoft Account).
3. Set **Scheduled Refresh** (if Import mode) to match source update cadence.
4. Package as a **Power BI App** and distribute to users/groups for governed sharing.

---

## Troubleshooting
- **Cannot connect**: verify server name, DB name, firewall IP, and auth mode. Test with SSMS/Azure Data Studio.
- **Login failed**: ensure the user exists in the DB (`CREATE USER ... FROM LOGIN ...`) and has `db_datareader` at minimum.
- **Slow refresh**: push heavy cleaning to SQL views; remove unused columns/rows; disable Auto Date/Time if not needed.
- **Missing visual (Scroller)**: install from AppSource in **Power BI Desktop → Get more visuals**.

---

## Deliverables
- `*.pbix` — Multi‑page Power BI report (men’s clothing).
- `sql/` — T‑SQL scripts for schema, sample data, and cleansing views.
- `docs/` — Connection guide (Database vs Microsoft Account), firewall steps, refresh notes.
- `assets/` — Backgrounds and brand assets used in the report.

---

## Suggested Folder Structure
```
/README.md
/report/Mens-Clothing-AzureSQL.pbix
/sql/01_schema.sql
/sql/02_seed_data.sql
/sql/03_views_clean.sql
/docs/connection-guide.md
/docs/firewall-setup.md
/assets/backgrounds/*.png
```

---

## Notes
- For production, consider **Azure AD** auth and **Private Link** or VPN for stricter network controls.
- Keep only necessary columns/rows in the model; prefer star schema (Products, Calendar, Sales).
- Version reports via source control for PBIX and SQL artifacts.


