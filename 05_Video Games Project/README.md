# Video Games Sales Analytics — Power BI + Amazon Athena (ODBC)

A production-style Power BI report built on top of **Amazon Athena** (via the Simba ODBC driver) using the classic *Video Game Sales* dataset.  
The project demonstrates end‑to‑end setup (AWS Glue → Athena → ODBC → Power BI), robust data cleaning (incl. “Year” fixes and unpivoting), a compact DAX layer, and a polished multi‑page report with AppSource visuals (e.g., **Violin Chart**).

---

## 📌 What’s inside

- **Pages (3)**
  1) **Global Overview** — Genre distribution across regions with a *Violin Chart*, region toggles, and high‑level KPIs.  
  2) **Regional / Genre Detail** — Region slicer + Year×Region matrix for NA/EU/JP/Other, with genre and platform drill.  
  3) **Trends & Comparisons** — Line trends (e.g., **Action vs Sports** over time), plus genre ranking / share.

- **Data source**: S3 → **AWS Glue Data Catalog** → **Amazon Athena** → **Power BI (ODBC/Simba)**  
- **Modeling**: Single fact + (optional) Calendar table for time intelligence  
- **Visuals**: Violin Chart (AppSource), Matrix, Line charts, KPI cards, Slicers, Bookmarks/Bookmark Navigator  
- **Ops**: Connection hardening (IAM, WG/Result location), filter‑pane theming, publish & share via app

> Screens and structures reflected here are aligned with the attached PBIX/PDF export (e.g., **Sales by Genre – Violin Chart by region**, Year×Region matrix, and Action/Sports time series).  

---

## 🗺️ Architecture

```
CSV in S3  →  AWS Glue Crawler  →  Glue Data Catalog
              ↓
          Amazon Athena
              ↓  (Simba ODBC / native connector)
           Power BI Desktop  →  Data model + DAX  →  Report pages
              ↓
        Power BI Service (App)  →  Sharing & governance
```

**Why Athena + ODBC?**  
- Serverless SQL over S3, quick to spin up; ODBC works well with Power BI Import mode for interactive UX.  
- Separates storage (S3) from compute (Athena) and keeps transformation capability close to the source.

---

## ⚙️ Setup (condensed)

1. **S3 & Data**
   - Create an S3 bucket (e.g., `s3://vg-sales`) and upload CSV(s) (columns like `Genre, Year, NA_Sales, EU_Sales, JP_Sales, Other_Sales, Global_Sales`).

2. **AWS Glue**
   - Create a **Crawler** over the bucket → populate **Glue Data Catalog** database & table with proper types.

3. **Athena**
   - Choose/confirm **Workgroup** and **Query result location** (another S3 path).  
   - Validate `SELECT * FROM vg_sales LIMIT 10;`.

4. **IAM**
   - Create an IAM user or role with **Athena**, **Glue**, and **S3** read permissions; generate access keys if using ODBC DSN auth.

5. **Client (Power BI)**
   - Install **Simba Amazon Athena ODBC** driver.  
   - **Option A – Get Data → Amazon Athena** (recommended)  
   - **Option B – Get Data → ODBC** and select your Athena DSN.

---

## 🧹 Data Prep (Power Query)

- **Types & naming**: enforce numeric types for sales columns; text for `Genre`, `Platform`; numeric for `Year`.
- **“Year” fixes**: coerce to whole number, handle blanks/strings, optionally shift pre‑1970/edge values into a catch‑all bucket.
- **Unpivot for region analysis** *(used on the slicer‑based page)*:  
  Convert wide `NA_Sales, EU_Sales, JP_Sales, Other_Sales` → long `Region, Sales` to power a single Region slicer and regional comparisons.
- **Trim/minify**: remove unused columns, deduplicate, lowercase headers, and ensure query folding where possible.

---

## 🧠 Measures (DAX highlights)

```DAX
-- Base
[Global Sales] = SUM(Fact[Global_Sales])
[Region Sales] = SUM(Fact[Sales])  -- long format after Unpivot

-- Genre rank & share
[Genre Rank by Global] =
RANKX(ALL('Fact'[Genre]), [Global Sales], , DESC, Dense)

[Region Share %] =
DIVIDE([Region Sales], CALCULATE([Global Sales], ALLSELECTED('Fact'[Region])))

-- YoY
[YoY Global Sales] =
VAR Prev = CALCULATE([Global Sales], DATEADD('Date'[Date], -1, YEAR))
RETURN DIVIDE([Global Sales] - Prev, Prev)
```

> For fast time intelligence, create a dedicated **Date** table (`CALENDAR(MIN(Year), MAX(Year))`) and mark it as a *date table*.

---

## 📊 Report Pages (as implemented)

1) **Global Overview**
   - **Sales by Genre — Violin Chart** (AppSource visual) faceted by Region (NA/EU/JP/Other) with **Global** summary.  
   - KPI cards: Total Global Sales, Top Genre, Top Region.  
   - Bookmark navigator for quick state toggles.

2) **Regional / Genre Detail**
   - **Year × Region Matrix**: NA/EU/JP/Other yearly totals; sorted and color‑encoded.  
   - Genre and Platform slicers for drill.

3) **Trends & Comparisons**
   - **Line trends**: e.g., **Action vs Sports** global sales across time with annotations.  
   - Secondary visuals: genre rank, regional share bars.

---

## 🎨 UX & Interactions

- **Bookmarks vs Slicer pattern**: Page 1 uses bookmarks (presets); Page 2 uses a single **Region slicer** (enabled by Unpivot).  
- **Filter pane**: styled cards, descriptive titles; spotlight tooltips on hover.  
- **AppSource visual**: **Violin Chart** configured with Genre (axis) × Region (legend) × Sales (values).

---

## 🚀 Publish & Share

- **Publish** to Power BI Service → create a **Workspace App**; add audiences and permissions.  
- Optional: certify the dataset, define sensitivity labels, and enable usage metrics.

---

## 📦 Deliverables

- `.pbix` with 3 pages and DAX measures  
- README (this file) + data‑prep notes  
- Power BI Service app (screens, access matrix)

---

## 🧩 Known considerations

- Athena + ODBC favors **Import** for snappy UX and AppSource visuals; consider **DirectQuery** only for near‑real‑time scenarios.  
- Region unpivot doubles row count; watch dataset size & refresh time.  
- Ensure IAM/WG/S3 locations are consistent across environments.

---

## 🗂️ Version History

- **v2** — Refined visuals and documentation to match the PBIX/PDF export:  
  *Violin Chart by Genre & Region, Year×Region matrix, Action/Sports line trends, and KPI blocks aligned with final screenshots.*

---

## ▶️ Quickstart

1. Set up Glue + Athena as above; verify a simple query.
2. In Power BI, connect via **Amazon Athena** (or **ODBC DSN**).
3. Apply the Power Query steps (types, unpivot), load to model.
4. Paste the DAX measures and lay out the three pages.
5. Publish to Service and share via a Workspace App.
