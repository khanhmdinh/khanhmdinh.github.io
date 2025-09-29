# Menswear Insights Hub (Azure SQL ➜ Power BI)

A production-ready Power BI report for menswear merchandising and pricing analytics. The project connects **Azure SQL Database** to **Power BI Desktop**, applies data cleaning both in **Azure SQL** and **Power Query**, and delivers brand- and price-led insights with DAX measures and curated visuals. It also documents environment setup (Azure firewall and connection modes) and sharing via Power BI Apps.

---

## I. Report Pages
View the Live Dashboard: https://app.powerbi.com/reportEmbed?reportId=d6338b69-bee6-402e-a5b1-6322f673a3eb&autoAuth=true&ctid=9f40849d-a657-43a5-85dc-4bd96886bad5

![](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/c13be453fcfbfddbf415cb43936a859391124be6/images/Menswear_Insights_Hub_Cover.png)
![](https://github.com/khanhmdinh/khanhmdinh.github.io/blob/bc2e69fac51da0ce7ca755b98e706cb0263c4949/images/Menswear_Insights_Hub.png)

---

## II. Architecture
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

---

## III. Connection & Setup
1. **Azure SQL**
   - Create/identify database and tables (import or restore from backup).
   - Add client IP to **Azure SQL firewall**.
2. **Power BI Connection Modes**
   - **Database** (SQL authentication): `Get Data → Azure → Azure SQL Database`.
   - **Microsoft Account** (Azure AD): use organizational account SSO.
3. **Parameters & Config**
   - Optional: define `ENV`/server/database parameters for promotion across DEV/TEST/PROD.

>This project was built based on what I learned from the course “13+ Power BI Portfolio Projects with DAX & SQL” by Insigh BI Solutions Pvt Ltd & KRISHAI Technologies Private Limited. I made some modifications and added new features to fit my own learning goals.
