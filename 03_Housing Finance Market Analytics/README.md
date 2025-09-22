# Power BI — Housing Finance Market Analytics (Google BigQuery)

A three‑page Power BI report analyzing housing market performance with **Google BigQuery** as the source. The project emphasizes clean modeling, pragmatic DAX, and clear storytelling through visuals.

> Pages and visuals summarized below align with the attached PBIX/PDF export. Screens and numeric cues (e.g., YoY by sales type, regional splits, house‑type pairs) were validated against the PDF.


## Report Pages

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


## Architecture

- **Source**: Google BigQuery datasets (import mode recommended).  
- **Storage/Model**: Power BI model with star schema.
- **Transformations**: SQL in BigQuery + Power Query for last‑mile cleanup.
- **Analytics**: DAX measures for KPIs, YoY deltas, medians, and ratios.
- **Distribution**: Publish to Power BI Service; workspace with scheduled refresh.

### Conceptual Data Model (suggested)
- **FactSales**: `purchase_price`, `offer_price`, `sales_type`, `house_type`, `region`, `city`, `sqm`, `sqm_price`, `age`, `interest`, `inflation`, `yield`, `date_key`, …  
- **DimDate**: full calendar (contiguous daily grain).  
- **DimRegion**, **DimCity**, **DimHouseType**, **DimSalesType**.

Relationships: `FactSales[date_key] → DimDate[date_key]`, `FactSales[region] → DimRegion[region]`, etc.


## Representative DAX (copy‑ready)

```DAX
-- Calendar (if you need to generate it in-model)
Calendar =
ADDCOLUMNS (
  CALENDAR ( DATE(1990,1,1), DATE(2035,12,31) ),
  "Year", YEAR([Date]),
  "Quarter", "Q" & FORMAT([Date], "Q"),
  "Month", FORMAT([Date], "MMMM"),
  "MonthNo", MONTH([Date])
)

Total Sales (Purchase) := SUM ( FactSales[purchase_price] )
Total Offer            := SUM ( FactSales[offer_price] )

Total Sales YTD :=
CALCULATE (
  [Total Sales (Purchase)],
  DATESYTD ( DimDate[Date] )
)

Total Sales YoY :=
CALCULATE ( [Total Sales (Purchase)], DATEADD ( DimDate[Date], -1, YEAR ) )

YoY Growth % :=
DIVIDE ( [Total Sales (Purchase)] - [Total Sales YoY], [Total Sales YoY] )

Median Price := MEDIAN ( FactSales[purchase_price] )

Median Price YoY :=
CALCULATE ( [Median Price], DATEADD ( DimDate[Date], -1, YEAR ) )

Median Price Δ % :=
DIVIDE ( [Median Price] - [Median Price YoY], [Median Price YoY] )

Avg SQM Price := AVERAGE ( FactSales[sqm_price] )
Avg SQM       := AVERAGE ( FactSales[sqm] )

Offer/Purchase Δ % :=
VAR offer = [Total Offer]
VAR buy   = [Total Sales (Purchase)]
RETURN DIVIDE ( offer - buy, buy )

Offer-to-SQM Ratio :=
AVERAGEX ( FactSales, DIVIDE ( FactSales[offer_price], FactSales[sqm] ) )
```


## BigQuery Setup (quick guide)

1. **Dataset & Tables**: Create dataset; load fact & dimension tables (CSV/Parquet/External).  
2. **Access**: Service account with `roles/bigquery.dataViewer` (and `jobUser` for scheduled refresh).  
3. **Power BI Desktop**:  
   - *Data > Get Data > Google BigQuery* (recommended Import).  
   - Select tables/views; optionally use **SQL statement** for server‑side shaping.  
4. **Power Query**: Normalize text casing, split/merge location fields, fill nulls, enforce numeric types (e.g., prices, SQM).  
5. **Modeling**: Mark `DimDate[Date]` as Date table; set 1:* relationships; hide technical keys.


## Report Design Notes

- Consistent **region palette** across pages; shared slicers for **Region, City, Sales Type, Area**.
- Use **paired columns** (Offer vs Purchase) to surface negotiation spread by **house_type**.
- Overlay **macro context** (inflation / interest / yield) for richer narratives.
- Add **tooltips** with YoY and Median deltas to speed ad‑hoc analysis.


## Refresh & Governance

- **Scheduled refresh** in Power BI Service (Gateway not required for BigQuery).  
- Parameterize project/dataset if you promote across tiers (Dev/Test/Prod).  
- Row‑Level Security (optional): region‑scoped roles filtering `DimRegion`.


## Success Criteria

- **Accuracy**: Measures reconcile to source totals (±0.1%).  
- **Performance**: < 2s median interaction on Import model; < 120 MB PBIX.  
- **Usability**: Page‑level KPIs + intuitive slicers; answers target exec & analyst personas.  
- **Explainability**: Key Influencers highlights top drivers of purchase_price.


## What You’ll Learn

- Connecting Power BI to **Google BigQuery**; modeling a star schema.  
- Building **YoY** and **Median**‑based insights with DAX.  
- Designing multi‑page reports with **consistent slicers** and clear comparisons.  
- Publishing & maintaining a governed, refreshable dataset.


## Assets

- **PBIX/PDF**: Housing report (3 pages: Overview, Sales Performance, House Type Analysis).  
- Background images & theme (screenshot).
