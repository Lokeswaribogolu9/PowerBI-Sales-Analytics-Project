# 01 — Data Model (Star Schema)

This project uses a **star schema**, the industry-standard way to structure data for Power BI. One central fact table holds transactions; dimension tables around it describe those transactions. One extra fact table (`Fact_Returns`) and one precomputed analytics table (`Customer_RFM`) add depth beyond a typical beginner project.

```
                         Dim_Date
                            |
                            |
   Dim_Product ---- Fact_Sales ---- Dim_Customer ---- Customer_RFM
                            |              |
                            |              |
                     Dim_SalesRep     (RFM joins 1:1
                            |          on CustomerID)
                            |
                      Fact_Returns
                    (joins on OrderID)

   Dim_Targets  (joined via DAX — see 03_DAX_MEASURES.md)
```

## Tables

### Fact_Sales (6,000 rows) — the center
One row = one order line.

| Column | Type | Description |
|---|---|---|
| OrderID | Whole Number | Unique order identifier |
| DateKey | Whole Number | Links to Dim_Date (format YYYYMMDD) |
| CustomerID | Whole Number | Links to Dim_Customer |
| ProductID | Whole Number | Links to Dim_Product |
| RepID | Whole Number | Links to Dim_SalesRep |
| Quantity | Whole Number | Units sold |
| Discount | Decimal | Discount applied (0 to 0.2) |
| SalesAmount | Decimal | Revenue after discount |
| CostAmount | Decimal | Cost of goods sold |
| Profit | Decimal | SalesAmount minus CostAmount |
| Region | Text | Denormalized region (also available via Dim_Customer) |

### Fact_Returns (360 rows) — returns/quality analysis
One row = one product return linked back to the original order.

| Column | Type | Description |
|---|---|---|
| ReturnID | Whole Number | Unique key |
| OrderID | Whole Number | Links to Fact_Sales[OrderID] |
| ReturnDateKey | Whole Number | Links to Dim_Date[DateKey] |
| ReasonCode | Text | e.g. DAMAGED, WRONG_ITEM |
| ReasonDescription | Text | Human-readable reason |
| QuantityReturned | Whole Number | Units returned |
| RefundAmount | Decimal | Amount refunded |

### Dim_Date (1,096 rows — 2023 to 2025)
| Column | Description |
|---|---|
| DateKey | Matches Fact_Sales[DateKey] / Fact_Returns[ReturnDateKey] |
| Date | Actual date |
| Year, Quarter, Month, MonthName, Day | Calendar breakdowns |
| WeekdayName, IsWeekend | Day-of-week info |
| YearMonth | e.g. "2024-03", used for trend charts |

### Dim_Product (100 rows)
| Column | Description |
|---|---|
| ProductID | Unique key |
| ProductName | Product name |
| SubCategory | e.g. "Laptops" |
| Category | e.g. "Electronics" (4 categories, 20 sub-categories) |
| UnitCost, UnitPrice | Base pricing |

### Dim_Customer (250 rows)
| Column | Description |
|---|---|
| CustomerID | Unique key |
| CustomerName | Full name |
| Segment | Consumer / Corporate / Small Business |
| Region | North America / Europe / Asia Pacific |
| Country, City | Location detail |

### Dim_SalesRep (15 rows) — new
| Column | Description |
|---|---|
| RepID | Unique key |
| RepName | Full name |
| Region | Region the rep sells in |
| Team | e.g. "APAC Sales" |
| HireDate | Date hired |

### Customer_RFM (250 rows) — new, precomputed analytics table
One row per customer, already scored for you. See `04_ADVANCED_ANALYTICS.md` for how this was built and how to interpret it.

| Column | Description |
|---|---|
| CustomerID | Links to Dim_Customer |
| LastPurchaseDate | Most recent order date |
| Recency_Days | Days since last purchase (as of end of dataset) |
| Frequency_Orders | Number of distinct orders |
| Monetary_Total | Total amount spent |
| R_Score, F_Score, M_Score | 1-4 quartile scores (4 = best) |
| RFM_Score | Combined score, e.g. "432" |
| Segment | Champions / Loyal Customers / New-Promising / Needs Attention / At Risk (High Value) / Lost-Churned |

### Dim_Targets (108 rows)
Monthly sales targets by Region. Joined using DAX (see `03_DAX_MEASURES.md`) rather than a direct relationship, because it matches on 3 columns at once (Year, Month, Region).

## Relationships to build in Power BI

| From | To | Type |
|---|---|---|
| Fact_Sales[DateKey] | Dim_Date[DateKey] | Many-to-One |
| Fact_Sales[ProductID] | Dim_Product[ProductID] | Many-to-One |
| Fact_Sales[CustomerID] | Dim_Customer[CustomerID] | Many-to-One |
| Fact_Sales[RepID] | Dim_SalesRep[RepID] | Many-to-One |
| Fact_Returns[OrderID] | Fact_Sales[OrderID] | Many-to-One |
| Fact_Returns[ReturnDateKey] | Dim_Date[DateKey] | Many-to-One |
| Customer_RFM[CustomerID] | Dim_Customer[CustomerID] | One-to-One |

All relationships filter in a **single direction** (from the "one" side to the "many" side), the Power BI default and best practice.

## Why this structure?

- **Fast performance** — star schemas are optimized for how Power BI's engine (VertiPaq) compresses and queries data.
- **Simple DAX** — measures like `SUM(Fact_Sales[SalesAmount])` automatically respect filters from any connected dimension table.
- **Realistic depth** — the returns table and RFM table mirror patterns you'll actually be asked to build in a real analyst job (loss/quality analysis, customer segmentation), not just a single flat sales table.
