# 🏠 House Market Analytics Dashboard  
### End-to-End BigQuery + Power BI Data Analytics Project

![Power BI](https://img.shields.io/badge/Tool-Power%20BI-yellow?logo=powerbi)
![BigQuery](https://img.shields.io/badge/Database-Google%20BigQuery-blue?logo=googlecloud)
![DAX](https://img.shields.io/badge/Language-DAX-orange)
![Status](https://img.shields.io/badge/Project-Completed-success)

---

## 📌 Project Overview

This project analyzes Denmark Housing Market data using **Google BigQuery** and **Power BI** to uncover trends in:

- Year-over-Year Sales Growth  
- Regional Performance  
- Offer vs Purchase Price behavior  
- Market Seasonality  
- Price per Square Meter (SQM)  
- Macro-economic impact (Inflation, Interest, Yield)

The objective was to build a fully interactive, business-ready dashboard that enables stakeholders to monitor housing performance across regions, house types, and time.

---

# 🔄 End-to-End Project Flow

## 1️⃣ Extract
- Raw housing dataset uploaded into **Google BigQuery**
- Connected BigQuery to Power BI using native connector

## 2️⃣ Transform
- Cleaned data using Power Query  
- Created calculated columns (Age, Offer Price)  
- Built time-intelligence DAX measures  
- Standardized formats and handled missing values  

## 3️⃣ Load
- Loaded structured dataset into Power BI Data Model  

## 4️⃣ Model
- Optimized relationships  
- Implemented time-intelligence logic  
- Used advanced DAX functions  
- Managed filter context effectively  

## 5️⃣ Visualize
Built 3 interactive report pages:

- 📊 House Market Overview  
- 📈 Sales Performance  
- 🏘 House Type & Macro Analysis  

---

# 🏗 Data Architecture

## Data Source
- Database: Google BigQuery  
- Visualization Tool: Power BI Desktop  
- Country: Denmark  

## Key Columns

| Column | Description |
|--------|------------|
| date | Transaction date |
| house_id | Unique property ID |
| house_type | Apartment, Villa, Farm, etc. |
| sales_type | Auction, Regular, Family Sale |
| purchase_price | Final transaction price |
| sqm | Area in square meters |
| sqm_price | Price per sqm |
| region | Denmark region |
| city | City |
| year_build | Construction year |
| %_change_between_offer_and_purchase | Negotiation percentage |

---

# 📊 Key Performance Indicators (KPIs)

| KPI | Description |
|------|------------|
| YOY Sales Growth | Year-over-year growth rate |
| Units Sold (Latest Yr & Qtr) | Recent market activity |
| Last 12 Month Sales | Rolling sales measure |
| TotalYTD Sales | Year-to-date sales |
| Median Sales Price Change | Region-wise median growth |
| Offer to SQM Ratio | Offer intensity indicator |
| Average Price per SQM | Region-level pricing benchmark |

---

# 💼 Business Questions & STAR Method Analysis

---

## 1️⃣ YOY Sales Growth by Sales Type

**Situation:** Stakeholders lacked clarity on yearly performance by sales channel.  
**Task:** Calculate year-over-year growth.  
**Action:** Created DAX using CALCULATE, YEAR, MAX, IF, BLANK.

```
YOY_Sales_Growth =
VAR CurrYearSales =
    CALCULATE(
        SUM(Housing[purchase_price]),
        YEAR(Housing[date]) = YEAR(MAX(Housing[date]))
    )

VAR PrevYearSales =
    CALCULATE(
        SUM(Housing[purchase_price]),
        YEAR(Housing[date]) = YEAR(MAX(Housing[date])) - 1
    )

RETURN
IF(
    PrevYearSales <> 0,
    (CurrYearSales - PrevYearSales) / PrevYearSales,
    BLANK()
)
