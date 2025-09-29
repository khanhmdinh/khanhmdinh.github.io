# Loan Default Risk Analytics – Power BI (Dataflow)

A production-style Power BI project that analyzes **loan default risk** using **Power BI Dataflows** as the upstream source, **Power Query** for shaping, and **DAX** for robust measures. The final solution delivers a **three‑page report** covering portfolio overview, applicant demographics & financial profile, and financial risk metrics.

---

## I. Report Pages:

View the Live Dashboard: https://app.powerbi.com/reportEmbed?reportId=fdf25695-abd8-4233-949e-5a1cbb949c9c&autoAuth=true&ctid=216e5950-5a9c-4dc3-96cf-437406f9c7a3
![](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/e0cf32c7b7f824139cbd0ebaff246f5beb1ab547/images/Loan_Default_Risk_Analytics_1.png)
![](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/e0cf32c7b7f824139cbd0ebaff246f5beb1ab547/images/Loan_Default_Risk_Analytics_2.png)
![](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/30144ea3bbaac22dddc62df27e3a409eea488e77/images/Loan_Default_Risk_Analytics_Financial_Risk_Metrics_3.png)

  1) *Loan Default – Overview* (high‑level KPIs & trends)  
  2) *Applicant Demographics & Financial Profile* (segment drill‑downs)  
  3) *Financial Risk Metrics* (YoY deltas, YTD breakdowns)
- **Distribution**: Published to Power BI Service, shared via **Apps** with workspace permissions

### Page 1 — Loan Default & Overview
- **Loan Amount by Purpose** (Home, Business, Education, Auto, Other)
- **Average Income by Employment Type** (Full‑time, Self‑employed, Part‑time, Unemployed)
- **Default Rate (%) by Employment Type**
- **Average Loan Amount by Age Group** (Teen, Adults, Middle‑Age Adults, Senior Citizens)
- **Default Rate (%) by Year** time trend

**Goal**: one‑glance portfolio health and default hotspots across purpose, employment, age, and time.

### Page 2 — Applicant Demographics & Financial Profile
- **Median Loan Amount by Credit Score Category** (Very Low, Low, Medium, High)
- **Average Loan (High Credit) by Age Group × Marital Status**
- **Total Loan by Credit Score Bins**
- **Loans by Education Type**
- **Has Mortgage / Has Dependents** splits

**Goal**: understand how demographics and financial posture correlate with ticket size and risk.

### Page 3 — Financial Risk Metrics
- **YoY Loan Amount Change** & **YoY Default Loans Change** lines
- **YTD Loan Amount** by Credit Score Bins × Marital Status
- **Slicers** such as **Income Bracket** and **Employment Type** to interactively segment risk

**Goal**: monitor trend dynamics (YoY/YTD) and slice risk by credit & household context.

---

## II. Architecture & Data Flow

```
Power BI Dataflow  →  Power BI Dataset (Import)  →  Report (3 pages)  →  App (sharing)
               Power Query (profiling/cleaning)   DAX (KPIs & time intel)
```

- **Source**: Power BI **Dataflow** hosts the standardized entities.  
- **Dataset**: Import mode with **incremental refresh** configured for scalable updates.  
- **Modeling**: Star‑like model (fact loans + dimensions such as CreditScoreBin, EmploymentType, AgeGroup, MaritalStatus, Education).  
- **Delivery**: Published to Service; app used for governed sharing.

---

## IV. Deliverables

- Power BI **PBIX** (model, measures, report)
- Power BI **Dataflow** entities (source tables)
- Documentation (this **README**), plus refresh & sharing instructions.

>This project was built based on what I learned from the course “13+ Power BI Portfolio Projects with DAX & SQL” by Insigh BI Solutions Pvt Ltd & KRISHAI Technologies Private Limited. I made some modifications and added new features to fit my own learning goals.
