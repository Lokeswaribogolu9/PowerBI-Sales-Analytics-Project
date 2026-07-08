# 📊 Sales & Revenue Analytics — Power BI Project

![status](https://img.shields.io/badge/status-ready%20to%20build-brightgreen) ![tool](https://img.shields.io/badge/tool-Power%20BI-F2C811) ![data](https://img.shields.io/badge/data-6%2C000%2B%20orders-blue) ![level](https://img.shields.io/badge/level-portfolio%20project-orange)

A complete, end-to-end Power BI analytics project: a realistic 3-year sales dataset, a proper star-schema data model, DAX measures (including time intelligence and ranking), RFM customer segmentation, a sales rep leaderboard, and a returns/quality analysis — all documented step by step so anyone can build the working dashboard in Power BI Desktop.

---

## Executive Summary

| Metric | Value |
|---|---|
| Total Sales (2023-2025) | **$6,429,922** |
| Total Profit | **$2,418,980** |
| Overall Profit Margin | **37.6%** |
| Total Orders | **6,000** |
| Total Refunds | **$228,953** |
| Return Rate | **6.0%** |
| Top-Performing Category | **Office Supplies** |
| Top-Performing Region | **Asia Pacific** |
| "Champion" Customers Identified | **51** (via RFM segmentation) |
| High-Value At-Risk Customers | **30** (via RFM segmentation) |

*(Figures computed directly from the dataset in this repo — see `data/raw/`.)*

## Business Questions This Project Answers

1. How is total sales revenue and profit trending over time, and where is it headed next quarter?
2. Which product categories, sub-categories, and regions drive the most revenue and profit?
3. Which sales reps are top performers, and how does each region's team compare?
4. Which customers are most valuable, and which high-value customers are at risk of churning?
5. How much revenue is lost to returns, and what are the most common return reasons?
6. Are we hitting our monthly sales targets by region?

## What This Project Demonstrates

- **Data modeling**: a proper star schema (1 primary fact table, 1 secondary fact table, 5 dimension tables, 1 precomputed analytics table)
- **DAX**: core aggregations, time intelligence (YTD, YoY, rolling averages), dynamic multi-column joins, `RANKX` leaderboards, `TOPN`
- **Advanced analytics**: RFM customer segmentation, returns/quality analysis, built-in forecasting
- **Dashboard design**: a 4-page report (Executive Overview, Product & Customer Detail, Sales Rep Performance, Customer Segmentation & Returns)
- **Project hygiene**: clean folder structure, documentation, and version control — the same habits used on real analytics teams

## Screenshots

_Add your dashboard screenshots here after building it in Power BI Desktop (see `screenshots/README.md`). Example:_

```markdown
![Executive Overview](screenshots/01_executive_overview.png)
![Sales Rep Performance](screenshots/02_sales_rep_performance.png)
![Customer Segmentation](screenshots/03_customer_segmentation.png)
```

## Project Structure

```
PowerBI-Sales-Analytics-Project/
├── README.md                          <- you are here
├── LICENSE
├── .gitignore
├── data/
│   ├── raw/                           <- source-of-truth CSVs, one per table
│   │   ├── Fact_Sales.csv
│   │   ├── Fact_Returns.csv
│   │   ├── Dim_Date.csv
│   │   ├── Dim_Product.csv
│   │   ├── Dim_Customer.csv
│   │   ├── Dim_SalesRep.csv
│   │   ├── Dim_Targets.csv
│   │   └── Customer_RFM.csv
│   └── processed/
│       └── Sales_Analytics_Dataset.xlsx   <- all 8 tables in one workbook (import this into Power BI)
├── docs/
│   ├── 01_DATA_MODEL.md               <- star schema + relationships
│   ├── 02_BUILD_GUIDE.md              <- step-by-step guide to build the report in Power BI Desktop
│   ├── 03_DAX_MEASURES.md             <- every DAX formula used, copy-paste ready
│   ├── 04_ADVANCED_ANALYTICS.md       <- RFM, ranking, and forecasting explained in plain English
│   └── 05_GITHUB_SETUP.md             <- how to push this project to your own GitHub
├── pbix/
│   └── README.md                      <- save your finished .pbix file here
└── screenshots/
    └── README.md                      <- save your dashboard screenshots here
```

## How to Use This Project

1. Read `docs/02_BUILD_GUIDE.md` and follow it in Power BI Desktop, using `data/processed/Sales_Analytics_Dataset.xlsx` as your data source.
2. Use `docs/01_DATA_MODEL.md` to build the relationships correctly.
3. Copy the formulas from `docs/03_DAX_MEASURES.md` into your report.
4. Read `docs/04_ADVANCED_ANALYTICS.md` to understand (and be able to explain in an interview) the RFM segmentation, leaderboard, and forecasting features.
5. Save your finished `.pbix` into `pbix/`.
6. Take screenshots, drop them into `screenshots/`, and update this README's screenshot section.
7. Follow `docs/05_GITHUB_SETUP.md` to push everything to your own GitHub repository.

## Dataset Summary

| Table | Rows | Description |
|---|---|---|
| Fact_Sales | 6,000 | Order-level transactions, 2023-2025 |
| Fact_Returns | 360 | Product returns linked to original orders |
| Dim_Date | 1,096 | Daily calendar table, 2023-2025 |
| Dim_Product | 100 | Products across 4 categories, 20 sub-categories |
| Dim_Customer | 250 | Customers across 3 regions, 3 segments |
| Dim_SalesRep | 15 | Sales reps across 3 regional teams |
| Dim_Targets | 108 | Monthly sales targets by region |
| Customer_RFM | 250 | Precomputed Recency/Frequency/Monetary segmentation, 1 row per customer |

This is a synthetic (fabricated) dataset generated with Python/pandas for learning and portfolio purposes — it does not represent a real company.

## Tech Stack

- **Power BI Desktop** — data modeling, DAX, dashboard design
- **Python (pandas)** — dataset generation and RFM segmentation logic
- **Git / GitHub** — version control and portfolio hosting

## License

MIT — free to use and adapt for your own learning or portfolio. See `LICENSE`.
