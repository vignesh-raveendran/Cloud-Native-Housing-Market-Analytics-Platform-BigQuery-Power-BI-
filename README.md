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

### 🖼 Dashboard Preview
* **📊 House Market Overview**


<img width="1256" height="743" alt="Screenshot 2026-02-16 204358" src="https://github.com/user-attachments/assets/59b92807-671b-415e-8547-0359a40b266c" />


  
* **📈 Sales Performance**
  

<img width="1300" height="722" alt="Screenshot 2026-02-16 210502" src="https://github.com/user-attachments/assets/3356734f-ca47-477e-8262-7ed65c9e259a" />


* **🏘 House Type Analysis**

<img width="1299" height="720" alt="Screenshot 2026-02-17 110801" src="https://github.com/user-attachments/assets/5db2a36f-5a3e-44f5-abe2-7ef23aaf00c8" />



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

```dax
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
```

**Result:**
- Auction sales showed positive growth (~29%)
- Family sales declined (~ -75%)


### 2️⃣ Median Sales Price Change by Region
* **Situation:** Average sales prices were heavily skewed by extreme outliers (e.g., luxury estates vs. very small apartments).
* **Task:** Calculate a robust metric to measure true regional price movement, resistant to these anomalies.
* **Action:** Engineered a custom DAX measure utilizing `MEDIANX` combined with dynamic year filtering to extract the year-over-year median price change.

```dax
Median Sales Price Change = 
VAR CurrMedianPrice = 
    MEDIANX(
        FILTER(Housing, YEAR(Housing[date (MM/DD/YYYY)].[Date]) = YEAR(MAX(Housing[date (MM/DD/YYYY)].[Date]))),
        Housing[purchase_price]
    )
VAR PreMedianPrice = 
    MEDIANX(
        FILTER(Housing, YEAR(Housing[date (MM/DD/YYYY)].[Date]) = YEAR(MAX(Housing[date (MM/DD/YYYY)].[Date])) - 1),
        Housing[purchase_price]
    )
RETURN
    IF(PreMedianPrice <> 0, (CurrMedianPrice - PreMedianPrice) / PreMedianPrice, BLANK())
```

**Result:**
- Identified regional stability differences across Denmark.

### 3️⃣ Offer Price vs Purchase Price
* **Situation:** Understand negotiation dynamics.
* **Task:** Derive Offer Price and analyze correlation.
```
Offer Price =
(100 * Housing[purchase_price]) /
(100 - Housing[%_change_between_offer_and_purchase])
```

* **Result:** Strong linear relationship observed between offer and purchase prices.

### 4️⃣ Units Sold in Latest Year & Quarter
```
Units sold in latest Year & Quarter =
CALCULATE(
    DISTINCTCOUNT(Housing[house_id]),
    YEAR(Housing[date]) = YEAR(MAX(Housing[date])) &&
    QUARTER(Housing[date]) = QUARTER(MAX(Housing[date]))
)
```

* **Result:** 77 units sold in latest quarter.


### 5️⃣ Last 12 Month Sales
```
Last 12 Month Sales =
CALCULATE(
    SUM(Housing[purchase_price]),
    DATESINPERIOD(
        Housing[date],
        MAX(Housing[date]),
        -12,
        MONTH
    )
)
```
* **Result:** Captured rolling sales momentum.

### 6️⃣ Sales by Region
```
Sales by Region =
CALCULATE(
    SUM(Housing[purchase_price]),
    ALLEXCEPT(Housing, Housing[region])
)
```
* **Result:** Zealand and Jutland dominate housing sales.

### 7️⃣ Offer to SQM Ratio
```
Offer to SQM Ratio =
DIVIDE(
    SUM(Housing[Offer Price]),
    SUM(Housing[sqm])
)
```

### 8️⃣ TotalYTD Sales
```
TotalYTD Sales =
TOTALYTD(
    SUM(Housing[purchase_price]),
    Housing[date]
)
```
### 📈 Key Insights
* Zealand generates the highest housing revenue
* Auction channel is the most volatile
* Family sales show declining trend
* Offer and purchase prices closely aligned
* Smaller regions show higher price volatility
* Interest rates influence villa and farm pricing more strongly

### 🧠 Learnings & Challenges
* **Advanced DAX:** `MEDIANX`, `TOTALYTD`, `DATESINPERIOD`, `ALLEXCEPT`, `DIVIDE`
* **Time Intelligence:** Managing filter context using `MAX(date)` was critical.
* **BigQuery Integration:** Connected cloud database directly into Power BI.
* **Outlier Management:** Median calculations provided better insights than averages.
* **Dashboard UX:** Clean slicers, logical visual flow, and business storytelling.

### 🏁 Recommendations
* Add predictive forecasting
* Integrate real-time housing feeds
* Include mortgage approval analysis
* Build ML model for price prediction


### 🛠 Tech Stack
* Google BigQuery
* Power BI Desktop
* DAX
* Power Query
* Data Modeling
* Time Intelligence

### ✍️ Author

**Vignesh Raveendran**
 -- *Data Engineer / Data Analyst*
* **LinkedIn:** [https://linkedin.com/in/yourprofile](https://linkedin.com/in/yourprofile)
* **Portfolio:** [https://yourportfolio.com](https://yourportfolio.com)
