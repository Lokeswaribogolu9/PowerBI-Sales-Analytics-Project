# 03 — DAX Measures Reference

Copy-paste these directly into Power BI. For each: right-click the relevant table in the Fields pane → **New measure** → paste the formula (everything after removing the leading table note) → press Enter.

---

## Core Measures (on Fact_Sales)

```dax
Total Sales = SUM(Fact_Sales[SalesAmount])
```

```dax
Total Profit = SUM(Fact_Sales[Profit])
```

```dax
Total Orders = DISTINCTCOUNT(Fact_Sales[OrderID])
```

```dax
Total Quantity = SUM(Fact_Sales[Quantity])
```

```dax
Profit Margin % = DIVIDE([Total Profit], [Total Sales], 0)
```

```dax
Average Order Value = DIVIDE([Total Sales], [Total Orders], 0)
```

---

## Time Intelligence (requires Dim_Date marked as a Date table — see 02_BUILD_GUIDE.md Part 4)

```dax
Sales Last Year = CALCULATE([Total Sales], SAMEPERIODLASTYEAR(Dim_Date[Date]))
```

```dax
Sales YoY % = DIVIDE([Total Sales] - [Sales Last Year], [Sales Last Year], 0)
```

```dax
Sales YTD = TOTALYTD([Total Sales], Dim_Date[Date])
```

```dax
Sales Rolling 3-Month Avg =
AVERAGEX(
    DATESINPERIOD(Dim_Date[Date], LASTDATE(Dim_Date[Date]), -3, MONTH),
    [Total Sales]
)
```
A smoothed trend line — plot this alongside `Total Sales` on the trend chart to show underlying momentum without month-to-month noise.

---

## Target Comparison (Dim_Targets is not directly related — joined dynamically)

```dax
Sales Target =
CALCULATE(
    SUM(Dim_Targets[SalesTarget]),
    FILTER(
        Dim_Targets,
        Dim_Targets[Year] = SELECTEDVALUE(Dim_Date[Year]) &&
        Dim_Targets[Month] = SELECTEDVALUE(Dim_Date[Month]) &&
        Dim_Targets[Region] = SELECTEDVALUE(Dim_Customer[Region])
    )
)
```

```dax
Target Variance = [Total Sales] - [Sales Target]
```

```dax
Target Achievement % = DIVIDE([Total Sales], [Sales Target], 0)
```

**Note:** `Sales Target` only returns a value when both Year/Month and Region are filtered together (e.g. a table broken out by month and region, or slicers narrowing to one region). This cross-filtered pattern is common in real analyst work.

---

## Ranking Measures (Sales Rep Leaderboard)

```dax
Sales Rank =
RANKX(
    ALL(Dim_SalesRep[RepName]),
    [Total Sales],
    ,
    DESC
)
```
Put this in a table alongside `Dim_SalesRep[RepName]` and `Total Sales` to rank every rep from 1 (highest sales) down.

```dax
Top Product by Sales =
CALCULATE(
    SELECTEDVALUE(Dim_Product[ProductName]),
    TOPN(1, ALLSELECTED(Dim_Product), [Total Sales], DESC)
)
```
Use in a Card visual to show whichever product is currently highest-selling, respecting any slicers/filters applied on the page.

---

## Discount / Pricing Insight

```dax
Total Discount Given = SUMX(Fact_Sales, Fact_Sales[Quantity] * RELATED(Dim_Product[UnitPrice]) * Fact_Sales[Discount])
```

```dax
Average Discount % = AVERAGE(Fact_Sales[Discount])
```

---

## Returns & Quality (on Fact_Returns)

```dax
Total Returns = COUNTROWS(Fact_Returns)
```

```dax
Total Refund Amount = SUM(Fact_Returns[RefundAmount])
```

```dax
Return Rate % = DIVIDE([Total Returns], [Total Orders], 0)
```
Shows what fraction of orders end up returned — a key quality/customer-satisfaction metric.

```dax
Net Sales After Returns = [Total Sales] - [Total Refund Amount]
```

---

## Customer Segmentation (on Customer_RFM — mostly precomputed, but these help you summarize it)

```dax
Champion Customer Count = CALCULATE(DISTINCTCOUNT(Customer_RFM[CustomerID]), Customer_RFM[Segment] = "Champions")
```

```dax
Average Customer Value = AVERAGE(Customer_RFM[Monetary_Total])
```

```dax
At Risk Customer Count = CALCULATE(DISTINCTCOUNT(Customer_RFM[CustomerID]), Customer_RFM[Segment] = "At Risk (High Value)")
```

---

## Tips for writing your own DAX later

1. Start simple: `SUM`, `AVERAGE`, `COUNT`, `DISTINCTCOUNT` cover most needs.
2. `CALCULATE` is the most powerful function — it changes the filter context. Think of it as "calculate this, but under these different conditions."
3. Always use `DIVIDE(numerator, denominator, 0)` instead of a plain `/` — it prevents divide-by-zero errors and lets you set a fallback value.
4. Red squiggly underline in the formula bar? Hover over it — Power BI tells you exactly which word it doesn't recognize.
5. `RANKX` and `TOPN` are what interviewers love to see — they show you can build leaderboards and "best/worst of" views, not just totals.
