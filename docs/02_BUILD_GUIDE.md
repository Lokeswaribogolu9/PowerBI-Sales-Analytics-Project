# 02 — Step-by-Step Guide: Build the Power BI Dashboard

This guide walks you through building the entire dashboard from zero, even if you've never opened Power BI before. Follow the steps in order. Each step tells you exactly where to click.

---

## Part 1: Install Power BI Desktop

1. Go to `https://powerbi.microsoft.com/desktop/` in your web browser.
2. Click **"Download free"**.
3. Choose the **Microsoft Store** install (easiest, auto-updates) or download the `.exe` installer directly.
4. Open the installer, click **Install**, wait, then **Launch**.
5. On first open, click **"Skip"** on the sign-in prompt — you only need an account later to publish online.

**Common mistake:** Power BI Desktop is **Windows-only**. On a Mac, use a Windows virtual machine, a Windows PC, or the browser-based Power BI Service instead.

---

## Part 2: Import the Data

1. Open Power BI Desktop.
2. Click **Home → Get Data → Excel workbook**.
3. Navigate to `data/processed/Sales_Analytics_Dataset.xlsx` inside this project and click **Open**.
4. In the **Navigator** window, check the box next to **all 8 sheets**: `Fact_Sales`, `Fact_Returns`, `Dim_Date`, `Dim_Product`, `Dim_Customer`, `Dim_SalesRep`, `Dim_Targets`, `Customer_RFM`.
5. Click **"Transform Data"** (not "Load") to open the Power Query Editor and sanity-check column types first.

### Quick data check in Power Query Editor

1. Click `Dim_Date` in the left panel. Confirm the `Date` column type is **Date** (check the small icon in the column header). Fix it if needed via the column header icon.
2. Click `Fact_Sales`. Confirm `SalesAmount`, `CostAmount`, `Profit`, `Discount` are **Decimal Number**, and `Quantity`, `OrderID`, `DateKey`, `CustomerID`, `ProductID`, `RepID` are **Whole Number**.
3. Click `Fact_Returns`. Confirm `RefundAmount` is **Decimal Number** and `QuantityReturned`, `OrderID`, `ReturnDateKey` are **Whole Number**.
4. Click `Customer_RFM`. Confirm `LastPurchaseDate` is **Date**, `Monetary_Total` is **Decimal Number**, and `Recency_Days`, `Frequency_Orders` are **Whole Number**.
5. Click **"Close & Apply"** (top-left, under Home).

**Common mistake:** If you clicked "Load" by accident, that's fine — revisit types anytime via **Home → Transform Data**.

---

## Part 3: Build the Data Model (Relationships)

1. Click the **Model view** icon on the left vertical bar (three connected boxes).
2. Spread the 8 tables out so you can see them clearly, with `Fact_Sales` roughly in the middle.
3. Create these relationships by **dragging** from one column to another (see `01_DATA_MODEL.md` for the full list):
   - `Fact_Sales[DateKey]` → `Dim_Date[DateKey]`
   - `Fact_Sales[ProductID]` → `Dim_Product[ProductID]`
   - `Fact_Sales[CustomerID]` → `Dim_Customer[CustomerID]`
   - `Fact_Sales[RepID]` → `Dim_SalesRep[RepID]`
   - `Fact_Returns[OrderID]` → `Fact_Sales[OrderID]`
   - `Fact_Returns[ReturnDateKey]` → `Dim_Date[DateKey]`
   - `Customer_RFM[CustomerID]` → `Dim_Customer[CustomerID]`
4. For each, a popup confirms **Cardinality** ("Many to 1" or "One to 1" for the RFM link) and **Cross filter direction** ("Single"). Click **OK**.
5. Leave `Dim_Targets` unconnected — it's joined dynamically with DAX (see `03_DAX_MEASURES.md`) since it matches on 3 columns at once.

**Common mistake:** Wrong columns connected? Click the relationship line and press **Delete**, then redo it.

Your model should look like a **star** with a couple of extra branches — `Fact_Sales` in the center, `Fact_Returns` and `Customer_RFM` hanging off related dimensions.

---

## Part 4: Mark the Date Table

1. Still in Model view, click on `Dim_Date`.
2. Go to the **Table tools** ribbon tab → click **"Mark as date table"** → confirm `Date` is the date column → **OK**.

This enables the time-intelligence DAX functions (YTD, YoY, rolling averages) used in `03_DAX_MEASURES.md`.

---

## Part 5: Create DAX Measures

1. Click **Report view** on the left bar (bar chart icon).
2. Right-click `Fact_Sales` in the Fields pane → **"New measure"**.
3. Type the measure name and formula from `03_DAX_MEASURES.md`, starting with `Total Sales`. Press **Enter**.
4. Repeat for every measure listed there — organize them under `Fact_Sales`, `Fact_Returns`, or `Customer_RFM` depending on which table they use.

**Common mistake:** Typos in column names are the #1 cause of DAX errors. Power BI underlines errors in red — hover to see what's wrong; check spelling and capitalization exactly.

---

## Part 6: Build the Dashboard — 4 Pages

Create each page by clicking the **"+"** tab at the bottom, then right-click the tab → **Rename**.

### Page 1: "Executive Overview"
- **4 KPI cards** across the top: `Total Sales`, `Total Profit`, `Profit Margin %`, `Return Rate %` (Card visual, drag each measure into the Fields well).
- **Line chart**: `Dim_Date[YearMonth]` on X-axis, `Total Sales` on Y-axis. Title: "Sales Trend Over Time".
  - Click the chart, open the **Analytics** pane (magnifying-glass-with-line icon) → **Forecast** → click **"Add"** → this adds a built-in trend forecast with a confidence band, no DAX required. Set forecast length to 3-6 periods.
- **Clustered bar chart**: `Dim_Product[Category]` on Y-axis, `Total Sales` on X-axis. Sort descending via "..." on the visual.
- **Donut chart**: `Dim_Customer[Region]` in Legend, `Total Sales` in Values.
- **Slicers**: `Dim_Date[Year]` and `Dim_Customer[Region]`.

### Page 2: "Product & Customer Detail"
- **Table**: `Dim_Product[Category]`, `Dim_Product[SubCategory]`, `Total Sales`, `Total Profit`, `Profit Margin %`.
- **Table**: Top customers — `Dim_Customer[CustomerName]`, `Total Sales`, `Total Profit`. Use the **Filter pane → Top N** filter (Top 10 by Total Sales).
- **Scatter chart**: `Dim_Product[UnitCost]` on X, `Dim_Product[UnitPrice]` on Y, `Category` as legend — shows pricing strategy across categories.

### Page 3: "Sales Rep Performance" (new)
- **Bar chart**: `Dim_SalesRep[RepName]` on Y-axis, `Total Sales` on X-axis, sorted descending — your leaderboard.
- **Table**: `RepName`, `Team`, `Total Sales`, `Total Orders`, `Sales Rank` (the RANKX measure from `03_DAX_MEASURES.md`).
- **Card**: `Top Rep Name` if you build that measure, or simply eyeball the #1 row in the sorted table.
- **Slicer**: `Dim_SalesRep[Team]`.

### Page 4: "Customer Segmentation (RFM) & Returns" (new)
- **Donut or bar chart**: `Customer_RFM[Segment]` in Legend/Axis, count of `CustomerID` in Values — shows how many customers fall in each RFM segment.
- **Table**: `Customer_RFM[Segment]`, `Monetary_Total` (summed), `Frequency_Orders` (averaged) — set the aggregation by clicking the field in the Values well and choosing **Average** or **Sum**.
- **Bar chart**: `Fact_Returns[ReasonDescription]` on Y-axis, count of `ReturnID` on X-axis — top return reasons.
- **Card**: `Return Rate %` measure.

### Formatting tips
- Select each visual → **Format** pane (paint roller) → give it a clear title.
- Use **View → Themes** for a consistent color palette across all 4 pages.
- Select multiple visuals with **Ctrl+Click**, then use **Format → Align** to line them up neatly.

**Common mistake:** Overcrowding a page. Keep each page focused on its theme — that's why this project uses 4 pages instead of 1 giant page.

---

## Part 7: Save Your File

1. Press **Ctrl + S**.
2. Save inside this project's `pbix/` folder as `Sales_Analytics_Dashboard.pbix`.
3. Keep saving as you go — Power BI does not autosave by default.

---

## Part 8 (Optional): Publish to Power BI Service

1. **Home → Publish** on the ribbon.
2. Sign in with a free Microsoft/Power BI account if prompted.
3. Choose **"My workspace"** → **Select**.
4. Click the link that appears to open your report online and get a shareable URL.

---

## What to put in your portfolio / resume

Take 3-4 screenshots of your finished dashboard (Windows: **Win + Shift + S**) and save them into `screenshots/`. Reference them in `README.md` so anyone visiting your GitHub repo sees the result without opening Power BI.
