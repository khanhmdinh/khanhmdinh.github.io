# Menswear Insights Hub (Azure SQL ➜ Power BI)

A production-ready Power BI report for menswear merchandising and pricing analytics. The project connects **Azure SQL Database** to **Power BI Desktop**, applies data cleaning both in **Azure SQL** and **Power Query**, and delivers brand- and price-led insights with DAX measures and curated visuals. It also documents environment setup (Azure firewall and connection modes) and sharing via Power BI Apps.

---

View the Live Dashboard: https://app.powerbi.com/reportEmbed?reportId=d6338b69-bee6-402e-a5b1-6322f673a3eb&autoAuth=true&ctid=9f40849d-a657-43a5-85dc-4bd96886bad5

![](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/42d6c2799be28dcfc87489185bdb56f3815572e7/images/Menswear_Insights_Hub_1.png)
![](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/25e7b0be992448b86f6025cdde4fa505f08e3019/images/Menswear_Insights_Hub_2.png)

---

## 1) Objectives
- Provide a clean, reusable semantic layer for discount, profit, and sales price analysis across brands.
- Ship an executive-ready report with ranked brand leaders/laggards and pricing benchmarks.
- Capture source setup so the report can be reproduced or migrated across environments.

## 2) Architecture
- **Source:** Azure SQL Database (Menswear dataset)
- **ETL / Data Prep:** Azure SQL (pre-clean), Power Query (shaping & types), DAX (business logic)
- **BI Layer:** Power BI Desktop → Power BI Service (App for distribution)
- **Access & Security:** Azure SQL firewall rules + Power BI workspace permissions

```
Azure SQL (tables/views)
   └─ Power BI Desktop
       ├─ Power Query (cleaning, types)
       ├─ Model (relationships, date table)
       ├─ DAX measures (pricing & profit KPIs)
       └─ Report pages (brands, pricing, profitability)
            └─ Publish → Power BI Service App
```

## 3) Data Preparation & Modeling
- **Azure SQL cleanup:** handle NULLs, standardize brand names, remove stray characters; create views for stable schemas.
- **Power Query:**
  - Enforce numeric types (Price, Discount %, Profit %) and text types (Brand, Category).
  - Remove unused columns and normalize headers (spaces → underscores).
  - Apply friendly formats (currency, percent, decimal).
- **Modeling:**
  - Star-like model: `FactProducts`/`FactPrices` with `DimBrand`, optional `DimCategory`, `DimDate`.
  - Date table with marked role for time-intelligence DAX.
- **Key DAX patterns:**
  - `Avg Discount %`, `Avg Profit %`, `Avg Sales Price`
  - Top-N rankings and % of total varieties
  - YoY deltas where the calendar is available

## 4) Report Structure & Key Insights
**Page: Brand Overview**
- **Available Brands** list for exploration.
- **Top 5 Brands by Average Discount %** (e.g., **NETPLAY**, **U.S. Polo Assn.**, **THE BEAR HOUSE**, **British Club**, **The Indian Garage Co**).
- **Top 5 Brands by # of Varieties** (e.g., **The Indian Garage Co**, **U.S. Polo Assn.**, **SNITCH**, **GAP**, **THE BEAR HOUSE**, **NETPLAY**).
- **Top 5 Brands by Average Profit %** (e.g., **Be Active X AG**, **Crown Threads**, **PUREZA**, **VEERYA QUMASH FASHION**, **SiliSoul Thinc**).
- **Bottom 5 Brands by Average Profit %** (e.g., **Allen Cooper**, **BASICS**, **American Eagle**, **iVOC**, **PAST MODERN**, **Trybest**).
- **Top 5 Brands by Average Sales Price** (e.g., **ARMANI EXCHANGE**, **BROOKS BROTHERS**, **SCOTCH & SODA**, **kingdom of white**, **Terra Luna**).

These artifact labels and rankings are visible in the attached PBIX export and inform the analytics narrative for discounting, assortment breadth, profitability, and premium pricing tiers. 

**Page: Company / Summary**
- KPIs for average discount, average profit, and pricing bands.
- Brand selector and page-level filters to switch context quickly.

**Page: Pricing & Profitability Deep Dive**
- Combined views of **Avg Discount %** vs **Avg Profit %** and **Avg Sales Price** by brand.
- Drill-down to SKU/variant level where applicable.

## 5) Connection & Setup
1. **Azure SQL**
   - Create/identify database and tables (import or restore from backup).
   - Add client IP to **Azure SQL firewall**.
2. **Power BI Connection Modes**
   - **Database** (SQL authentication): `Get Data → Azure → Azure SQL Database`.
   - **Microsoft Account** (Azure AD): use organizational account SSO.
3. **Parameters & Config**
   - Optional: define `ENV`/server/database parameters for promotion across DEV/TEST/PROD.

## 6) Data Quality & Governance
- **Type safety:** enforced numeric/percentage data types for metrics.
- **Column hygiene:** normalized headers, removed unused columns.
- **Validation:** side-by-side checks in Azure SQL vs. Power BI (record counts, aggregates).
- **Performance:** reduced cardinality columns, removed visuals not in use, minimized bi-directional filters.

## 7) Deliverables
- 🟡 `Menswear_Insights_Hub.pbix`
- 🟡 Published **Power BI App** for governed sharing
- 🟡 This README (MD + PDF)

## 8) How to Run
1. Open `.pbix` → **Transform data** to confirm Power Query steps and data types.
2. Update **Data source settings** if the server or database changed.
3. Refresh locally; validate KPIs against SQL (e.g., Avg Discount %, Avg Profit %).
4. Publish to the appropriate workspace; configure **App** audiences.
5. (Optional) Add RLS roles if needed for brand/category-level access.

## 9) Success Criteria
- 📈 Accurate KPI calculations (discount, profit, price) across all brands.
- ⚡ Refresh completes < 5 min on DEV-sized data; no broken visuals.
- ✅ App distributed to target audience; stakeholders can filter by brand and drill into pricing and profit questions.

## 10) Notes from the PBIX PDF
The PDF export shows the **Available Brands** explorer plus ranked visuals for discounts, varieties, profits (top & bottom), and premium pricing leaders; use these to validate your own environment after connecting to Azure SQL.

>This project was built based on what I learned from the course “13+ Power BI Portfolio Projects with DAX & SQL” by Insigh BI Solutions Pvt Ltd & KRISHAI Technologies Private Limited. I made some modifications and added new features to fit my own learning goals.
