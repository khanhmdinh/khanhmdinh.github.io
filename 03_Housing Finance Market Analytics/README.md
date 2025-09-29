# Power BI — Housing Finance Market Analytics (Google BigQuery)

Build an end‑to‑end analytics solution for the housing & mortgage market to monitor **sales value & units, price dynamics, regional performance, and product/type mix**, and to support pricing/marketing decisions.

> **This report is grounded in the uploaded project overview and assets.** It documents the end-to-end approach (BigQuery → Power BI), the semantic model, core DAX, visuals, and an **Insights & Actions** section suitable to close a case‑study.

## 1) Report Pages

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

---

## 2) Project Environment & Architecture
- **Source.** BigQuery dataset (housing transactions & attributes). SSO connection from Power BI.
- **Staging/Cleansing.** SQL transforms in **BigQuery** for data understanding and basic fixes; **Power Query** for type/format standardization and column hygiene. fileciteturn5file0
- **Semantic model.** Star‑like model centered on **FactHousing** with conformed dimensions (*Date, Region, City, Area, House Type, Sales Type*).
- **Publishing.** Power BI Service (workspace, app distribution); datasets scheduled refresh; usage metrics.
- **(Optional)** Incremental Refresh for large datasets (RangeStart/RangeEnd) and RLS by Region/City if required by sharing policy.

---

## 3) Scope & Project Steps
1) **Business understanding** → identify questions: market growth, YoY change, regional hotspots, type mix shifts, and pricing dispersion.
2) **Data mapping & ingestion** → connect BigQuery, validate column dictionary, profile ranges & nulls. fileciteturn5file0
3) **Modeling** → define grain, relationships, and role dimensions (Date).
4) **DAX** → create robust measures for YoY/R12, price levels, units sold, and ratios.
5) **Storytelling** → compose pages (*Overview → Drill‑down → Type analysis*) with consistent design.
6) **Performance & governance** → optimize model, formatters, and (optionally) RLS; set refresh SLAs.
7) **Validation & sign‑off** → reconcile totals and trend sanity checks; publish app.

---

## 4) Data Sources & Dictionary (Key Columns)
**Core columns (excerpt).** `date`, `quarter`, `house_id`, `house_type`, `sales_type` (new vs resale), `year_build`, `purchase_price`, `%_change_between_offer_and_purchase`, `no_rooms`, `sqm`, `sqm_price`, `address`, `zip_code`, `city`, `area`, `region`, `nom_interest_rate%`, `dk_ann_infl_rate%`, `yield_on_mortgage_credit_bonds%`. fileciteturn5file0

**Examples of BigQuery SQL for data understanding.** Average purchase price; one‑off data correction (e.g., standardizing `SQM`). **Power Query** used for final type casting and cleaning prior to model load. fileciteturn5file0

---

## 5) Data Model (Star Schema)
- **FactHousing (grain: house_id, at transaction date).** `purchase_price`, `house_type`, `sales_type`, `sqm`, `sqm_price`, `%_change_offer_purchase`, `year_build`, `region`, `city`, `area`, `no_rooms`, interest/inflation/yield context fields.
- **DimDate** (`Date`, `Year`, `Quarter`, `Month`), **DimRegion**, **DimCity**, **DimArea**, **DimHouseType**, **DimSalesType**.
- **Relationships.** Single‑direction from dimensions to FactHousing (1:*); active Date relationship on `date`.

**Bus matrix.** Process (*Sales value, Units, Pricing, Market mix*) × Dimensions (*Date, Region, City, Area, House Type, Sales Type*).

---

## 6) DAX Measure Catalog (cleaned & production‑ready)

> Naming & formatting aligned to best practices; each measure is self‑contained and compatible with role dimensions.

```DAX
-- Core totals & counts
Sales Value := SUM(FactHousing[purchase_price])
# Transactions := DISTINCTCOUNT(FactHousing[house_id])

-- YoY growth (value)
Sales Value YoY % :=
VAR Curr = [Sales Value]
VAR Prev = CALCULATE([Sales Value], DATEADD(DimDate[Date], -1, YEAR))
RETURN IF(NOT ISBLANK(Prev), DIVIDE(Curr - Prev, Prev))

-- Units in latest Year & Quarter
Units (Latest YQ) :=
CALCULATE(
    [# Transactions],
    YEAR(DimDate[Date]) = YEAR(MAX(DimDate[Date])) &&
    QUARTER(DimDate[Date]) = QUARTER(MAX(DimDate[Date]))
)

-- Rolling 12 months
Sales Value R12 :=
CALCULATE([Sales Value], DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -12, MONTH))

-- Region contribution (context‑aware)
Sales by Region :=
CALCULATE([Sales Value], ALLEXCEPT(FactHousing, FactHousing[region]))

-- Price‑level statistics
Median Price := MEDIAN(FactHousing[purchase_price])
Average SQM Price := AVERAGE(FactHousing[sqm_price])

-- Offer price reconstructed from % change
Offer Price :=
DIVIDE(100 * [Sales Value], 100 - SUM(FactHousing[%_change_between_offer_and_purchase]))

-- Price change YoY (median)
Median Price YoY % :=
VAR CurrMed =
    MEDIANX(
        FILTER(ALL(DimDate), YEAR(DimDate[Date]) = YEAR(MAX(DimDate[Date]))),
        FactHousing[purchase_price]
    )
VAR PrevMed =
    MEDIANX(
        FILTER(ALL(DimDate), YEAR(DimDate[Date]) = YEAR(MAX(DimDate[Date])) - 1),
        FactHousing[purchase_price]
    )
RETURN IF(NOT ISBLANK(PrevMed), DIVIDE(CurrMed - PrevMed, PrevMed))

-- Structural attributes
Age (Years) := ABS(YEAR(MAX(DimDate[Date])) - AVERAGE(FactHousing[year_build]))

-- Ratios & mix
Offer to SQM Ratio := DIVIDE([Offer Price], SUM(FactHousing[sqm]))
New Sales Value := CALCULATE([Sales Value], FactHousing[sales_type] = "new")
Resale Sales Value := CALCULATE([Sales Value], FactHousing[sales_type] = "resale")
New Share % := DIVIDE([New Sales Value], [Sales Value])
```

> The measure names map to those listed in the project overview (YoY, median price change, units in latest Y/Q, last‑12‑month sales, region sales, YTD, average SQM, age, offer‑to‑SQM). fileciteturn5file0

---

## 7) Report Pages & Visual Design

### Page 1 — Market Overview & Sales Performance
- **KPI cards:** *Sales Value, Sales Value YoY %, # Transactions, Median Price, Average SQM Price*.
- **Trends:** Area/line for *Sales Value R12*; bar/ribbon for *New vs Resale*; heat by *Region contribution*.
- **Interactions:** Cross‑filter by *Sales Type* and *Region*; drill to *City/Area*. fileciteturn5file0

### Page 2 — House Type Analysis
- **Breakdowns:** *House Type × Sales Type* matrix; distribution of *SQM* and *no_rooms*; *Offer‑to‑SQM ratio* scatter to spot outliers.
- **Context:** Tooltips with Median Price YoY % by type; slicers for *Year/Quarter*. fileciteturn5file0

---

## 8) Insights & Actions (decision‑ready)

**1) Growth concentrates by region and type.** A subset of regions contributes a disproportionate share of **Sales Value** and **YoY growth**, with **[Top House Types]** leading unit gains.  
**Action.** Focus marketing and inventory sourcing on **Top Regions × Leading Types**; replicate successful listings/playbooks.  
**Target.** **+X% Sales Value** in focus regions; maintain **YoY > network median**.

**2) New vs Resale cycle diverges.** **New** sales outpace **Resale** (or vice‑versa) in the latest R12; pricing dispersion appears higher in **[Type/Area]**.  
**Action.** Rebalance listing mix and adjust pricing guidance; use **Offer‑to‑SQM ratio** to flag under‑ or over‑priced segments.  
**Target.** Improve **conversion (# Transactions)** and **Median Price YoY** stability in targeted segments.

**3) Type & size economics matter.** **SQM** and **no_rooms** drive willingness‑to‑pay differently by area; **Median Price** drifts are not uniform across types.  
**Action.** Curate bundles by **Type×Area**; publish “sweet‑spot” size bands in sales guidance.  
**Target.** ↑ **Average SQM Price** where demand supports it; reduce discounting variance.

**4) Pricing discipline via reconstructed offers.** The **Offer Price** back‑calculation highlights gaps between offers and realized **purchase_price**.  
**Action.** Coach agents on realistic offer bands (by type/region); alert when **Offer‑to‑SQM** deviates beyond threshold.  
**Target.** Narrow **Offer→Purchase delta**; stabilize **Median Price YoY %**.

**5) Macro context as guardrails.** Where **nominal rate / inflation / mortgage bond yields** rise, **YoY** and **units** typically soften.  
**Action.** Adjust price guidance and campaign timing to macro signals; emphasize types/regions showing resilience.  
**Target.** Preserve **R12 Sales Value** and **YoY** outperformance vs network average.

> Replace bracketed targets with the current values from your model; each action should have an **owner**, **ETA**, and a **KPI** tracked on the Decision Board.

---

## 9) Performance, Security, Governance
- **Model hygiene:** correct data types; disable Auto Date/Time; display folders for measures; consistent format strings.
- **Performance:** consider **Incremental Refresh** for larger BigQuery tables; summarization tables for heavy visuals.
- **Security:** optional RLS by Region/City; sensitivity labels for PII (addresses).

---

## 10) Validation & Sign‑off
- **Reconciliation:** Sales Value & counts vs BigQuery queries (±0.5%).  
- **Sanity checks:** price distribution by type/region; SQM outlier filters; Offer→Purchase logic verified on samples.  
- **UAT:** 6–10 business scenarios confirmed by stakeholders.

---

## 11) Appendix — Supporting SQL, Power Query, and DAX

**BigQuery (examples)**
```sql
-- Average purchase price overview
SELECT AVG(purchase_price) AS avg_purchase_price
FROM `project.dataset.housing`;

-- Example standardization (illustrative)
UPDATE `project.dataset.housing`
SET sqm = 100
WHERE sqm IS NULL; -- or a specific data-fix scenario
```

**Power Query (type casting & cleaning)**
```m
let
  Source = Excel.CurrentWorkbook(){[Name="Housing"]}[Content],
  Renamed = Table.TransformColumnNames(Source, Text.Proper),
  Typed = Table.TransformColumnTypes(Renamed, {
    {"date", type date}, {"purchase_price", type number}, {"sqm", Int64.Type},
    {"sqm_price", type number}, {"%_change_between_offer_and_purchase", type number}
  }),
  Trimmed = Table.TransformColumns(Typed, {{"address", Text.Trim, type text}})
in
  Trimmed
```

>This project was built based on what I learned from the course “13+ Power BI Portfolio Projects with DAX & SQL” by Insigh BI Solutions Pvt Ltd & KRISHAI Technologies Private Limited. I made some modifications and added new features to fit my own learning goals.
