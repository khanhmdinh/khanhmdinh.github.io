# End-to-End Azure Databricks Lakehouse – Traffic & Roads Analytics

## 1) Project Overview
A Databricks implementation of the **Medallion architecture** (Bronze → Silver → Gold) for UK **traffic counts** and **roads** datasets. This README covers the **foundational setup**—ADLS Gen2 containers, **Unity Catalog** objects, and Delta tables—so the pipeline can ingest from a landing zone to Bronze and transform onward to Silver/Gold.

![](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/fa5291dbc0d0b5317adc7a09dd55172104d01cd5/images/Azure_Databricks_project-architecture.drawio.png)
View the Live Dashboard: 

## 1) Background & Scope
**Context.** The organization needs trustworthy, near‑current insight into traffic flow and road capacity to support **congestion management, maintenance planning, and EV adoption strategy**. Historically, reporting was fragmented, slow to refresh, and not lineage‑aware.

**Goals.**
- Establish a **single source of truth** on Delta Lake (Bronze→Silver→Gold) with governed access via **Unity Catalog**.
- Deliver **daily/near‑daily** analytics on traffic intensity and road utilisation to inform **signal timing, lane/shoulder operations, and maintenance scheduling**.
- Provide a **self‑service dashboard** for Operations/Planning with clear business metrics and definitions.

**In‑Scope.** Traffic counts & roads reference; batch‑style incremental ingestion; curated Gold tables for BI.  
**Out‑of‑Scope.** Real‑time streaming SLAs; advanced forecasting; multi‑touch attribution across external systems.

**Decisions.**
- **Operations**: identify top congested links; prioritise signal retiming & incident response.
- **Planning**: select corridors for capacity/maintenance; evaluate EV infrastructure needs.
- **Execs**: monitor KPI trends and investment effect (pre/post actions).

---

## 2) Data Structure Overview
**Core entities.**
- `fact` (traffic at link/hour granularity): counts by vehicle type, date/hour, region, link geometry.
- `dim` (conformed): `date`, `region`, `road`, `channel/type` (road category), `location`.

**ERD (overview).**
- See `./docs/erd.png` for the simplified star schema with **conformed dimensions** reused across processes.
- Grain:
  - **Traffic**: `order_line` equivalent → **link‑hour**.
  - **Roads**: link attributes (length, category/type).

**Lineage path.**
- **Landing** (raw CSV) → **Bronze** (Delta raw + `extract_time`) → **Silver** (cleaned + `transform_time`) → **Gold** (business‑ready + `load_time`).

---

## 3) Architecture & Key Behaviours
- **Storage**: ADLS Gen2; Unity Catalog for governed `catalog.schema.table` namespaces and permissions.
- **Ingestion**: Databricks **Auto Loader (cloudFiles)** with explicit schema; **checkpointed** for exactly‑once semantics; `trigger(availableNow=True)` for batch‑style incremental runs.
- **Transformations**: Structured Streaming over Delta for **incremental** Bronze→Silver→Gold; idempotent logic; parameterised by `env` (dev/test/prod).
- **Governance/Security**: Unity Catalog access controls; environment‑scoped external locations; no secrets hard‑coded.
- **Orchestration**: Databricks **Workflows** chaining notebooks with failure isolation and central config.
- **Observability**: query/job names, checkpoints, lineage timestamps per layer for audit & SLA checks.

---

## 4) Business Metrics (definitions & where they live)
| Metric | Definition | Layer | Rationale |
|---|---|---|---|
| **Total Motor Vehicles** | `cars + buses + lgv + hgv + electric_vehicles_count` | **Silver/Gold** | Core volume indicator for load & capacity decisions. |
| **EV Share (%)** | `electric_vehicles_count / motor_vehicles_count` | **Silver/Gold** | Tracks EV adoption and charging demand planning. |
| **Vehicle Intensity (veh/km)** | `motor_vehicles_count / length_km` | **Gold (`gold_traffic`)** | Normalises flow by link length to highlight **pressure hotspots**. |
| **Peak Hour Factor** | `max(hourly_count) / avg(hourly_count)` within day/region/link | **Gold** | Identifies peaking; informs **signal timing**. |
| **Congested Links (Top N)** | Links ranked by **Vehicle Intensity** (and/or % over threshold) | **Gold / Dashboard** | Target list for **ops actions**. |
| **Category Contribution** | Volume by `road_type` / category | **Gold** | Investment mix across road classes. |

> **Lineage/quality signals**: `extract_time` (Bronze), `transform_time` (Silver), `load_time` (Gold) allow **batch‑to‑batch** comparisons and SLA monitoring.

---

## 5) Insights (business‑first, quantified where available)
Each insight pairs **metric → story → action**. Replace bracketed values with current run outputs from the dashboard/SQL.

### 5.1 Congestion Hotspots (Pressure)
- **What**: The top **5%** of links by **Vehicle Intensity** account for **[X]%** of total motor‑vehicle flow.
- **So‑what**: Prioritise **signal retiming** and **incident response** for these links; consider **temporary hard shoulder** during peaks.
- **Quantification**: Reducing average intensity by **[Δ veh/km]** on hotspots yields **[Y]%** improvement in corridor travel time (proxy from historical elasticity).

### 5.2 EV Adoption & Infrastructure
- **What**: **EV Share** has reached **[X]%** overall; corridors **[R1, R2]** show **[+Y pp]** vs average.
- **So‑what**: Prioritise **charging infrastructure** in high‑EV corridors; plan **peak‑hour load management**.
- **Quantification**: Target **[N]** new chargers per **[km]** to support **[Z]%** YoY EV growth.

### 5.3 Peaking & Scheduling
- **What**: **Peak Hour Factor** exceeds **[X]** in **[regions]** between **[HH:00–HH:00]**.
- **So‑what**: Shift maintenance windows; evaluate **staggered start times** with large employers; retime **green splits**.
- **Quantification**: A **[Δ%]** reduction in peak factor could free **[N] lane‑hours/week**.

> **Evidence of incremental value (engineering)**: a validation run ingested a new traffic CSV and **doubled Bronze rows 18,546 → 37,092**, with distinct `extract_time` between runs and no duplicates due to checkpointed Auto Loader. This proves **incremental, exactly‑once** behaviour and enables trustworthy trend deltas release‑over‑release.

---

## 6) Recommendations & 90‑Day Plan (Impact × Effort)
| # | Recommendation | Owner | Effort | Impact | ETA | KPI Target |
|---|---|---|---|---|---|---|
| 1 | Deploy *Hotspot Playbook*: retime signals + incident‑response rules on top 5% links | Ops | M | High | 4–6 w | ↓ Peak Hour Factor by **[Δ]** |
| 2 | EV Infra Pilot on high‑adoption corridors | Planning | M | Med | 6–8 w | + EV Share **[pp]** |
| 3 | Maintenance scheduling optimiser (avoid peak blocks) | Ops | L | Med | 2–3 w | ↑ On‑time maint. **[Δ%]** |

> Track ROI via time‑saved (hrs) × blended labour cost + travel‑time savings from intensity reduction on hotspots.

---

## 7) Implementation Details (Databricks)
**Bronze (Landing → Bronze).**
- Auto Loader (`cloudFiles`), explicit schema, separate `schemaLocation` & `checkpointLocation` per feed.
- Adds `extract_time = current_timestamp()` for **auditable runs**.
- Batch‑style: `trigger(availableNow=True)` — ingests backlog then stops (ideal for scheduled runs).

**Silver (Bronze → Silver).**
- Structured Streaming reads over Delta; **dropDuplicates()**, `fillna` for null handling, column hygiene.
- Business features:
  - `electric_vehicles_count = ev_car + ev_bike`
  - `motor_vehicles_count = cars + buses + lgv + hgv + electric_vehicles_count`
  - `transform_time = current_timestamp()`

**Gold (Silver → Gold).**
- Light business layer; key calc: `vehicle_intensity = motor_vehicles_count / length_km`
- Adds `load_time = current_timestamp()`

**Orchestration (Workflows).**
- DAG: **Load to Bronze → Silver:Traffic → Silver:Roads → Gold:Finalize**
- Centralised `%run` config; `env` passed via widgets; downstream tasks gated on predecessor success.

**Monitoring & Ops.**
- Query/Job names; checkpoints; row deltas by `extract_time/transform_time/load_time`.
- Recovery via checkpoints; access governed in **Unity Catalog**.

---

## 8) Adoption & Measurement
- **Usage**: MAU/WAU of dashboard; top pages/queries; stakeholder training sessions.
- **Value tracking**: time‑to‑insight reduction; peak‑hour incident time saved; EV infra utilisation.
- **Governance**: quarterly access reviews; cost tags on Jobs/SQL Warehouse; SLA dashboards on refresh time.

---

## 9) Caveats & Assumptions
- Coverage of traffic counts may vary by region/month; missing intervals are imputed conservatively.
- Currency/timezone standardisation not applicable to counts but **local clock effects** (DST) are normalised.
- EV classification is **as‑reported**; misclassification risk exists for early datasets.

---

## 10) How to Reproduce
1) **Provision**: import repo; configure Unity Catalog (`catalog.schema`), external locations, and secrets (Key Vault).  
2) **Run**: execute notebooks in order via **Workflows**; provide `env` parameter; verify checkpoints.  
3) **Serve**: publish **Databricks SQL** or **Power BI** dashboard on **Gold**; apply role‑based filters if required.

---

## Repo Structure
```
/notebooks
  02_Load_to_Bronze.ipynb
  03_Silver_Traffic_Transformations.ipynb
  04_Common.ipynb
  05_Silver_Roads_Transformations.ipynb
  06_Gold_Final_Transformations.ipynb
/workflows (job JSON or notes)
/docs
  erd.png
  metrics.md (optional)
  overview.png (optional)
README.md
```

**Attribution & Source Notes.** The pipeline design, medallion flow, incremental behaviour validation (18,546 → 37,092), lineage timestamps, and notebook/workflow breakdown are derived from the project PDF and implementation notes in this repository.
```

>This project was built based on what I learned from the course "Azure Databricks end to end project with Unity Catalog CICD" by Shanmukh Sattiraju. I made some modifications and added new features to fit my own learning goals.
