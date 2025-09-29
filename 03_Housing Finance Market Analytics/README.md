# Power BI — Housing Finance Market Analytics (Google BigQuery)

A three‑page Power BI report analyzing housing market performance with **Google BigQuery** as the source. The project emphasizes clean modeling, pragmatic DAX, and clear storytelling through visuals.

> Pages and visuals summarized below align with the attached PBIX/PDF export. Screens and numeric cues (e.g., YoY by sales type, regional splits, house‑type pairs) were validated against the PDF.

## I. Report Pages

View the Live Dashboard: https://app.powerbi.com/reportEmbed?reportId=8be8e820-fa1e-42c0-828a-11afb976c0e3&autoAuth=true&ctid=216e5950-5a9c-4dc3-96cf-437406f9c7a3
![](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/8a47bd066381b874acd9aa52b30d934d44a30e58/images/Housing_Finance_Market_Analytics_1.png)
![](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/8a47bd066381b874acd9aa52b30d934d44a30e58/images/Housing_Finance_Market_Analytics_2.png)

### 1) House Market Overview
**Purpose:** Executive snapshot of market momentum and regional signals.  
**Key visuals & KPIs**
- **YoY Sales Growth by Sales Type** — auction, regular_sale, other_sale, family_sale (auction positive; regular_sale negative in current snapshot).  
- **Offer vs Purchase Price** — side‑by‑side / scatter to study negotiation deltas.
- **Median Sales Price Change by Region** — Jutland, Fyn & islands, Zealand, Bornholm (−0.1 ↔ 0.1 range shown).
- **Units Sold (Latest Year & Quarter)** — KPI cards.
- Global slicers: **Region, City, Sales Type, Area**.

### 2) Sales Performance
**Purpose:** Drill‑down into sales volume, prices, and regional share.  
**Key visuals & KPIs**
- **Sales by Region** — e.g., Zealand ≈ 95bn, Jutland ≈ 81bn, Fyn & islands ≈ 15bn.
- **Average Price per SQM by Region** — ~20.85K (Zealand), ~13.62K (Fyn & islands), ~13.5K (Jutland), ~10.6K (Bornholm).
- **Key Influencers (purchase_price)** — e.g., *Age 2–16* segment associated with higher values (illustrative per data).
- **Offer‑to‑SQM ratio by Sales Type** — regular_sale (~15K), other_sale (~14K), family_sale (~12K), auction (~11K).
- **Date table grid** (Year/Quarter/Month/Day) with **TotalYTD Sales**.

### 3) House Type Analysis
**Purpose:** Compare pricing, size, and macro overlays by **house_type**.  
**Key visuals**
- **Avg Offer vs Purchase Price** by type (Farm, Apartment, Townhouse, Villa, Summerhouse).  
- **Avg Inflation / Interest / Yield** by type (macro overlay for context).  
- **Avg SQM & SQM Price** by type (e.g., Apartment SQM price is the highest among types in current snapshot).


## II. Architecture

- **Source**: Google BigQuery datasets (import mode recommended).  
- **Storage/Model**: Power BI model with star schema.
- **Transformations**: SQL in BigQuery + Power Query for last‑mile cleanup.
- **Analytics**: DAX measures for KPIs, YoY deltas, medians, and ratios.
- **Distribution**: Publish to Power BI Service; workspace with scheduled refresh.

### III. Conceptual Data Model
- **FactSales**: `purchase_price`, `offer_price`, `sales_type`, `house_type`, `region`, `city`, `sqm`, `sqm_price`, `age`, `interest`, `inflation`, `yield`, `date_key`, …  
- **DimDate**: full calendar (contiguous daily grain).  
- **DimRegion**, **DimCity**, **DimHouseType**, **DimSalesType**.

Relationships: `FactSales[date_key] → DimDate[date_key]`, `FactSales[region] → DimRegion[region]`, etc.

## IV. BigQuery Setup (quick guide)

1. **Dataset & Tables**: Create dataset; load fact & dimension tables (CSV/Parquet/External).  
2. **Access**: Service account with `roles/bigquery.dataViewer` (and `jobUser` for scheduled refresh).  
3. **Power BI Desktop**:  
   - *Data > Get Data > Google BigQuery* (recommended Import).  
   - Select tables/views; optionally use **SQL statement** for server‑side shaping.  
4. **Power Query**: Normalize text casing, split/merge location fields, fill nulls, enforce numeric types (e.g., prices, SQM).  
5. **Modeling**: Mark `DimDate[Date]` as Date table; set 1:* relationships; hide technical keys.


## V. Report Design Notes

- Consistent **region palette** across pages; shared slicers for **Region, City, Sales Type, Area**.
- Use **paired columns** (Offer vs Purchase) to surface negotiation spread by **house_type**.
- Overlay **macro context** (inflation / interest / yield) for richer narratives.
- Add **tooltips** with YoY and Median deltas to speed ad‑hoc analysis.

>This project was built based on what I learned from the course “13+ Power BI Portfolio Projects with DAX & SQL” by Insigh BI Solutions Pvt Ltd & KRISHAI Technologies Private Limited. I made some modifications and added new features to fit my own learning goals.
