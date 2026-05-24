# Global-Hospitality-Analytics-Dashboard
End-to-end hospitality analytics dashboard analysing hotel chain performance, data quality metrics, and revenue insights across global datasets — built with Python, SQL, and Power BI.

# Global Hotel Industry — Data Analytics Capstone Project

**End-to-end hospitality analytics project covering data creation, cleaning, SQL analysis, forecasting, and Power BI dashboard — built to demonstrate 3+ years of data analyst skills in the hospitality domain.**

---

## Project Overview

This capstone project simulates a real-world data analyst workflow for the global hotel industry. It covers the full pipeline from raw data creation to executive-level Power BI dashboards, including Python data cleaning, SQL analysis with window functions, and 2026 revenue forecasting using Facebook Prophet and Linear Regression.

The dataset covers **5,500 records** across **25 countries**, **12 hotel chains**, **49 sub-brands**, and **7 years (2019–2025)** — with key hospitality KPIs including RevPAR, ADR, Occupancy %, and GOP %.

---

## Tools and Technologies

| Tool | Purpose |
|------|---------|
| Python (Pandas, NumPy, Matplotlib, Seaborn) | Data cleaning, EDA, forecasting |
| Facebook Prophet | Global revenue forecasting |
| Scikit-learn (Linear Regression) | Chain and country level forecasting |
| MySQL | SQL analysis with window functions and CTEs |
| Power BI Desktop (DAX, Power Query) | 5-page interactive dashboard |
| Excel | Star schema dataset creation |
| Git and GitHub | Version control and portfolio |

---

## Dataset Summary

| Metric | Value |
|--------|-------|
| Total Fact Rows | 5,500 |
| Countries | 25 |
| Cities | 250 (10 per country) |
| Hotel Chains | 12 |
| Sub-Brands | 49+ |
| Years Covered | 2019 to 2025 |
| Tables | Fact_Revenue + 4 Dimension tables (Star Schema) |

### Countries Covered

Thailand, France, UAE, UK, USA, Japan, Italy, Malaysia, India, Spain, China, South Korea, Australia, Singapore, Turkey, Mexico, Brazil, Germany, Netherlands, Greece, Portugal, Indonesia, Vietnam, Saudi Arabia, Morocco

### Hotel Chains

Marriott International, Hilton Worldwide, IHG, Accor, Hyatt, Wyndham, Choice Hotels, Shangri-La, Lotte, Minor Hotels, Radisson Hotel Group, Banyan Tree

### KPIs in Dataset

Revenue USD, ADR (Average Daily Rate), RevPAR (Revenue Per Available Room), TRevPAR (Total RevPAR), Occupancy %, GOP % (Gross Operating Profit)

---

## Project Architecture — Star Schema

```
                    Dim_Date
                       |
Dim_Chain ---- Fact_Revenue ---- Dim_Hotel
                       |
                  Dim_Location
```

| Table | Type | Rows | Purpose |
|-------|------|------|---------|
| Fact_Revenue | FACT | 5,500 | All KPI numbers |
| Dim_Hotel | DIMENSION | 4,640 | Hotel details |
| Dim_Chain | DIMENSION | 12 | Chain info |
| Dim_Location | DIMENSION | 250 | City and country |
| Dim_Date | DIMENSION | 2,557 | Full calendar 2019-2025 |

---

## Step 1 — Raw Data (hotel_raw_dirty.csv)

The raw dataset contains **8 types of intentional data quality issues** to simulate real-world messy data:

| Issue Type | Details | Rows Affected |
|-----------|---------|---------------|
| Missing Values | Null Google_Rating, Reviews_Count, GOP%, ADR, Segment, Currency | 503 nulls |
| Duplicate Rows | Exact duplicates + near duplicates (same hotel, different Transaction_ID) | 100 rows |
| Wrong Data Types | Year as "FY2022", "2,023", Star_Rating as "Five", "N/A" | 90 rows |
| Outliers | Revenue 15x-50x inflated, Google_Rating above 5.0, Occupancy above 100% | 65 rows |
| Inconsistent Formatting | "thailand", "THAILAND", "Thiland", "Q 1", "QTR1", "quarter1" | 120 rows |
| Impossible Values | RevPAR greater than ADR (mathematically impossible), Year 2099, Star_Rating 7 | 60 rows |
| Negative Values | Revenue_USD as negative numbers | 30 rows |
| Whitespace | Leading and trailing spaces in City, Sub_Chain, Transaction_ID with special characters | 125 rows |

---

## Step 2 — Python Data Cleaning and EDA

**File:** `Hotel_Data_Cleaning_EDA.ipynb`

### Cleaning Steps

| Step | Method | Formula Used |
|------|--------|-------------|
| Fix null Google_Rating | Fill with median per Star_Rating group | groupby(Star_Rating).transform(median) |
| Fix null Reviews_Count | Fill with median per Sub_Chain group | groupby(Sub_Chain).transform(median) |
| Fix outliers | Cap at 1st and 99th percentile | clip(lower=p01, upper=p99) |
| Fix data types | Convert Year, Star_Rating, Revenue to correct types | astype(int) |
| Fix formatting | Strip whitespace, standardize country names | str.strip(), str.title() |
| Add derived columns | Year_Label, Occupancy_Category, Revenue_per_Review | pd.cut(), map(), division |

### EDA Charts (10 charts auto-saved as PNG)

1. Revenue by Year — COVID dip in 2020 clearly visible
2. Revenue by Chain — which chain earns most
3. Top 15 Countries by Revenue
4. RevPAR and ADR by Star Rating
5. Occupancy Trend 2019-2025 — recovery story
6. Country x Year Occupancy Heatmap
7. Google Rating vs Revenue Scatter
8. Revenue by Segment (Luxury vs Economy)
9. Quarterly Revenue Pattern — Q3 peak season
10. Top 15 Cities by RevPAR

---

## Step 3 — SQL Analysis (MySQL)

**File:** `hotel_sql_queries.sql`

13 queries covering basic to advanced SQL techniques:

| Query | Type | Business Question |
|-------|------|------------------|
| 1 | Basic Aggregation | Revenue, RevPAR, ADR, Occupancy by Year |
| 2 | GROUP BY + HAVING | Chains with Avg RevPAR above $200 |
| 3 | RANK Window Function | Countries ranked by revenue within each region |
| 4 | LAG Window Function | Year-over-year revenue growth by chain |
| 5 | CTE + Running Total | Cumulative revenue quarter by quarter |
| 6 | Subquery | Hotels with RevPAR above their country average |
| 7 | DENSE_RANK Window | Top 3 cities per country by occupancy |
| 8 | CTE + CASE | Classify hotels as Top, Good, Average, Below Average |
| 9 | NTILE Window | Hotels split into revenue quartiles |
| 10 | Multi-CTE | COVID drop percentage and recovery status per chain |
| 11 | FIRST_VALUE Window | Best and worst quarter per country |
| 12 | Complex GROUP BY | Luxury vs Economy full KPI comparison |
| 13 | Stored Procedure | CALL GetChainKPIs('CHN01') — reusable KPI report |

### Key SQL Concepts Used

RANK(), DENSE_RANK(), NTILE(), LAG(), FIRST_VALUE(), CTE (WITH clause), CASE WHEN, NULLIF(), Stored Procedures, Subqueries, HAVING

---

## Step 4 — Revenue Forecasting 2026

**File:** `Hotel_Revenue_Forecasting_2026.ipynb`

### Models Used

**Facebook Prophet (Global level)**
- Trained on quarterly revenue 2019-2024
- Handles COVID as special event (changepoint)
- Generates 90% confidence interval
- Output: 2025 and 2026 quarterly predictions

**Linear Regression (Chain and Country level)**
- Trained on yearly revenue per chain
- Predicts 2026 revenue capped at 25% above 2024
- Splits yearly forecast into quarters using historical weights

### Model Accuracy (Backtesting 2023-2024)

| Metric | Result |
|--------|--------|
| MAE | Mean Absolute Error in USD millions |
| MAPE | Below 15% — acceptable for business forecasting |
| R2 | Above 0.70 — model explains most variation |

### Forecast Output

`hotel_forecast_2026.csv` — 3 scopes (Global, Chain, Country) with Forecast_Revenue_M, Lower_Bound_M, Upper_Bound_M for each quarter

---

## Step 5 — Power BI Dashboard (5 Pages)

**File:** `Hospitality_Dashboard.pbix`

### Page 1 — Executive Summary
KPI cards (Total Revenue $19.3B, Avg RevPAR $196, Avg ADR $309, Avg Occupancy 61.5%, Avg GOP 27.5%), Revenue trend line 2019-2025 showing COVID impact, Segment donut chart, Top 5 Countries bar, Year/Quarter/Region/Revenue_Type slicers

### Page 2 — Revenue Analysis
Full area line chart 2019-2025, Chain revenue comparison bar (Marriott vs Hilton vs IHG etc.), Quarterly revenue pattern (Q3 peak season confirmed), Chain x Year revenue matrix with conditional formatting

### Page 3 — Geographic View
Azure Maps bubble map (25 countries, bubble size = revenue), Revenue by Region donut (Asia Pacific 39.5%, Europe 33.2%), Top 15 Countries bar chart, Country x Year Occupancy heatmap showing 2020 COVID dip

### Page 4 — Chain Deep Dive
Chain to Sub-Chain revenue matrix with drilldown, RevPAR vs Occupancy scatter bubble chart by Segment, Chain and Sub-Brand treemap (all 49 sub-brands visible), Top hotels detail table

### Page 5 — 2026 Forecast
Forecast vs Actual KPI comparison cards, Line chart showing Chain/Country/Global forecast trends 2025-2026, Chain-level 2026 revenue bar chart, Detailed forecast table with confidence bounds

### DAX Measures Created

```
Total Revenue = SUM(Fact_Revenue[Revenue_USD])
Avg RevPAR = AVERAGE(Fact_Revenue[RevPAR_USD])
Avg ADR = AVERAGE(Fact_Revenue[ADR_USD])
Avg Occupancy % = AVERAGE(Fact_Revenue[Occupancy_%])
Avg GOP % = AVERAGE(Fact_Revenue[GOP_%])
LY Revenue = CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(Dim_Date[Date]))
YoY Revenue % = DIVIDE([Total Revenue]-[LY Revenue],[LY Revenue],0)
GOP Amount = SUMX(Fact_Revenue, Fact_Revenue[Revenue_USD]*Fact_Revenue[GOP_%]/100)
Forecast 2026 = CALCULATE(SUM(hotel_forecast_2026[Forecast_Revenue_M]), hotel_forecast_2026[Year]=2026)
Actual 2024 = CALCULATE(SUM(Fact_Revenue[Revenue_USD]), Fact_Revenue[Year]=2024)
Growth % = DIVIDE([Forecast 2026]-[Actual 2024],[Actual 2024],0)
```

---

## Key Business Insights

1. **COVID Impact** — Global hotel revenue dropped 65% in 2020 from $3.2B to $1.2B per quarter. Recovery took until 2023 to surpass pre-COVID levels.

2. **Top Chain** — Hyatt and Marriott International lead in total revenue. Hyatt has the highest Avg RevPAR at $276 confirming luxury positioning.

3. **Peak Season** — Q3 (July-September) is consistently the highest revenue quarter across all 25 countries confirming summer as global travel peak.

4. **Geographic Leader** — Asia Pacific generates 39.5% of global hotel revenue, driven by Thailand, Japan, China, and Australia.

5. **Luxury vs Economy** — Luxury segment generates 62% of total revenue from only 35% of hotels. Economy hotels win on volume but lose on RevPAR.

6. **2026 Forecast** — Global hotel revenue projected to grow 8-12% from 2024 to 2026, driven by Asia Pacific recovery and Middle East expansion (Saudi Vision 2030).

7. **Best City for RevPAR** — New York, London, Paris, Dubai, and Tokyo lead global RevPAR rankings confirming premium pricing in gateway cities.

---

## File Structure

```
hospitality-analytics-project/
│
├── data/
│   ├── hotel_raw_dirty.csv              # Raw data with 8 types of quality issues
│   ├── hotel_fact_cleaned.csv           # Cleaned fact table (output of Notebook 1)
│   ├── hotel_star_schema_5500rows.xlsx  # Full star schema (5 sheets)
│   └── hotel_forecast_2026.csv         # 2026 revenue forecast output
│
├── notebooks/
│   ├── Hotel_Data_Cleaning_EDA.ipynb    # Python cleaning and 10 EDA charts
│   └── Hotel_Revenue_Forecasting_2026.ipynb  # Prophet + sklearn forecasting
│
├── sql/
│   └── hotel_sql_queries.sql           # 13 SQL queries + CREATE TABLE + INSERT
│
├── dashboard/
│   └── Hospitality_Dashboard.pbix      # Power BI 5-page dashboard
│
├── docs/
│   ├── 01_Project_Overview.pdf
│   ├── 02_Dataset_StarSchema.pdf
│   ├── 03_Python_Cleaning_EDA.pdf
│   ├── 04_SQL_Analysis.pdf
│   ├── 05_Forecasting_2026.pdf
│   ├── 06_Python_Formulas_Reference.pdf
│   └── 07_PowerBI_Dashboard_Guide.pdf
│
└── README.md
```

---

## How to Run

**Python Notebooks:**
```bash
pip install pandas numpy matplotlib seaborn prophet scikit-learn openpyxl
jupyter notebook
```
Run `Hotel_Data_Cleaning_EDA.ipynb` first, then `Hotel_Revenue_Forecasting_2026.ipynb`

**SQL:**
```sql
-- In MySQL Workbench:
-- File > Open SQL Script > hotel_sql_queries.sql
-- Click Run All (lightning bolt)
-- All tables created and loaded automatically
```

**Power BI:**
- Open `Hospitality_Dashboard.pbix` in Power BI Desktop
- Update data source paths if needed
- Refresh data

---

## About

**Analyst:** Pratik Chorghe

**Experience:** 3+ years Data Analyst | Hospitality Domain (GIATA GmbH — 20,000+ global hotel records)

**Connect:** [LinkedIn](https://linkedin.com/in/pratikchorghe) | [GitHub](https://github.com/pratikchorghe)

---

*This project was built as a capstone to demonstrate end-to-end data analyst skills — from raw data to executive dashboard — in the hospitality domain.*
