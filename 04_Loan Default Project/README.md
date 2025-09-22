# Power BI — Loan Default Analytics (Dataflow Source)

A hands‑on Power BI project that builds a **three‑page report** on loan defaults using **Power BI Dataflows** as the data source. You’ll practice data profiling, cleaning, validation, DAX, and refresh strategies (scheduled & incremental), and publish/share securely.

---

## Table of Contents
- [Overview](#overview)
- [Learning Outcomes](#learning-outcomes)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Dataset](#dataset)
- [Data Preparation with Dataflows](#data-preparation-with-dataflows)
- [Power BI Desktop](#power-bi-desktop)
  - [Data Understanding & Profiling](#data-understanding--profiling)
  - [Data Cleaning](#data-cleaning)
  - [Modeling](#modeling)
  - [Core DAX Measures](#core-dax-measures)
- [Report Pages](#report-pages)
- [Refresh Strategy](#refresh-strategy)
  - [Scheduled Refresh](#scheduled-refresh)
  - [Incremental Refresh](#incremental-refresh)
- [Validation Checklist](#validation-checklist)
- [Publishing & Sharing](#publishing--sharing)
- [Deliverables](#deliverables)
- [Suggested Folder Structure](#suggested-folder-structure)
- [Notes](#notes)

---

## Overview
- **Source:** Power BI **Dataflow** (curated tables for applications, accounts, repayments, products, and demographics).
- **Goal:** Analyze loan performance and credit risk; highlight default trends and drivers.
- **Output:** A polished **3‑page report**:
  1. **Loan Default — Overview** (executive KPIs)
  2. **Applicant Demographics & Financial Profile**
  3. **Financial Risk Metrics** (time trends, cohort & driver exploration)

---

## Learning Outcomes
You will learn to:
- Build and parameterize **Power BI Dataflows** as a governed, reusable data source.
- Perform **data profiling**, **cleaning**, and **validation** before modeling.
- Create a **semantic model** with relationships, calculated columns, and **DAX measures**.
- Design effective visuals including **line charts**, **ribbon charts**, and a **decomposition tree**.
- Configure **Scheduled** and **Incremental Refresh** (interview‑favorite topic).
- Publish to Service and **share via Apps** with proper permissions.

---

## Architecture
```
Data Sources  ──►  Power BI Dataflow (staging/cleansing)  ──►  Power BI Desktop (Import)
                                             │
                                             └─►  Power BI Service (Dataset + Report) ──► App (Sharing)
```

---

## Prerequisites
- Power BI Desktop (latest) on Windows.
- Access to a Power BI workspace where **Dataflows** and **Datasets** can be created.
- Appropriate gateway (if on‑prem sources are used; not needed for cloud demo).
- Sample **Loan Default** dataset (tables for applications, customer profile, payments, products).

---

## Dataset
Typical fields:
- **Applications:** `ApplicationID`, `CustomerID`, `ApplicationDate`, `LoanAmount`, `Term`, `InterestRate`, `Purpose`  
- **Accounts/Status:** `LoanID`, `OriginationDate`, `CurrentStatus`, `ChargeOffDate`  
- **Repayments:** `PaymentDate`, `DueAmount`, `PaidAmount`, `DaysPastDue`  
- **Demographics/Financial:** `Age`, `EmploymentType`, `Income`, `DTI`, `Region`, `Education`

Target label examples: `DefaultFlag`, `ChargedOff`, or derived from status and days‑past‑due.

---

## Data Preparation with Dataflows
1. **Create a Dataflow** in your workspace and connect to the raw source(s).
2. Use **Power Query Online** to:
   - Enforce data types; standardize text (trim, clean, case).
   - Replace sentinel strings (e.g., `'NA'`, `'N/A'`, `'Unknown'`) with `null`.
   - Conform date formats; derive calendar attributes (Year, Month, Quarter).
   - Normalize money fields to Decimal; validate non‑negative amounts.
3. **Output Entities** for: `Applications`, `Accounts`, `Repayments`, `Customers`, `Products`.
4. **Refresh** the dataflow and **Connect** to it from Power BI Desktop (Get Data ▶ Power Platform ▶ **Dataflows**).

> Using Dataflows centralizes cleaning and enables reuse across reports.

---

## Power BI Desktop

### Data Understanding & Profiling
- View **Column Distribution/Quality/Profile**.
- Check null rates, distinct counts, min/max for numeric and date fields.

### Data Cleaning
- Remove redundant staging columns after deriving final fields (keeps model lean).
- Handle outliers (e.g., interest > 100% or negative balances).

### Modeling
- Create **one‑to‑many** relationships (e.g., `Customers 1─* Applications`, `Applications 1─* Repayments`).  
- Add a **Date** table and mark as **Date Table** for robust time intelligence.

### Core DAX Measures
Illustrative DAX you can adapt to your column names:

```DAX
Total Loans := DISTINCTCOUNT(Applications[LoanID])

Total Principal := SUM(Applications[LoanAmount])

Defaults := CALCULATE(
    DISTINCTCOUNT(Applications[LoanID]),
    FILTER(Applications, Applications[CurrentStatus] = "Default")
)

Default Rate % := DIVIDE([Defaults], [Total Loans])

On-Time Payments :=
CALCULATE(
    COUNTROWS(Repayments),
    FILTER(Repayments, Repayments[DaysPastDue] <= 0)
)

Delinquency 30+ :=
CALCULATE(
    DISTINCTCOUNT(Applications[LoanID]),
    FILTER(Applications, Applications[MaxDaysPastDue] >= 30)
)

Avg DTI := AVERAGE(Customers[DTI])

Revenue (Interest) := SUMX(
    Repayments,
    Repayments[PaidAmount] - Repayments[PrincipalComponent]
)
```

> Add time‑intelligence variants (MTD/QTD/YTD) once your Date table is wired up.

---

## Report Pages

### 1) Loan Default — Overview
- **KPIs:** Total Loans, Defaults, **Default Rate %**, Total Principal, Interest Revenue.
- Cards + KPI tiles; trend of Default Rate over time (line chart).

### 2) Applicant Demographics & Financial Profile
- **Demographic breakdowns:** Default Rate by Age band, Employment type, Region, Education.
- **Financial profiles:** Average DTI, Income bands, Loan Purpose vs Default Rate.
- Use column/stacked charts and slicers for drill‑down.

### 3) Financial Risk Metrics
- **Line charts** for delinquency buckets over time (30/60/90+).  
- **Ribbon chart** to show which purposes/segments lead default rate across months.  
- **Decomposition tree** (Explain by): Purpose → Region → Employment → DTI band.

---

## Refresh Strategy

### Scheduled Refresh
- Publish the PBIX; in the Service, set dataset **credentials** to the Dataflow connection.
- Configure refresh frequency aligned to dataflow refresh cadence (e.g., hourly/daily).

### Incremental Refresh
1. In Desktop, define `RangeStart` and `RangeEnd` parameters (Date/Time).
2. Apply a filter on the fact table date (e.g., `PaymentDate` **between** `RangeStart` and `RangeEnd`).  
3. In **Modeling ▶ Incremental refresh**: choose a policy (e.g., store last 5 years, refresh last 7 days).  
4. Publish; the first refresh loads the historical window, later refreshes only the **increment**.

> Pair incremental refresh with dataflow partitions for scalable pipelines.

---

## Validation Checklist
- KPI totals match source/system of record for a known date range.
- Default Rate by month equals manual SQL/PQ calculation.
- Filters/slicers don’t silence measures (watch inactive relationships).
- Time intelligence correct after marking Date table.
- Spot check 10 random loans from raw source to visuals (status & amounts).

---

## Publishing & Sharing
- Publish to the intended workspace.
- Configure **dataset refresh** and **dataflow refresh** schedules.
- Package as a **Power BI App**; grant access to users/groups (view‑only).  
- Document assumptions and measure definitions in a **Report Guide** page or `docs/`.

---

## Deliverables
- `Loan-Default.pbix` — 3‑page report with measures and model.
- Dataflow entities (exported JSON or documented steps).
- `docs/refresh-policy.md` — scheduled & incremental refresh setup.
- `docs/model-diagram.png` — relationship diagram.
- `docs/measure-catalog.md` — business definitions of all KPIs.

---

## Suggested Folder Structure
```
/README.md
/report/Loan-Default.pbix
/dataflow/export/*.json
/docs/refresh-policy.md
/docs/model-diagram.png
/docs/measure-catalog.md
/assets/backgrounds/*.png
```

---

## Notes
- Keep the model star‑shaped; separate fact (Repayments/Applications) from dimensions (Customers, Date, Product).
- Hide technical columns from the report view; expose only user‑friendly fields.
- Adopt a consistent naming convention for measures (prefix with category, or suffix with `%`/`Amt`).


