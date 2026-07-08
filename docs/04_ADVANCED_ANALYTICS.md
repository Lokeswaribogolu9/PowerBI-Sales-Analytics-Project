# 04 — Advanced Analytics Explained

This project goes beyond a basic "sales by category" dashboard. This doc explains the three advanced techniques included, in plain English, so you can explain them confidently in an interview.

---

## 1. RFM Customer Segmentation

**What it is:** A classic marketing-analytics technique that scores every customer on 3 dimensions:

- **R — Recency**: how recently did they buy? (fewer days = better)
- **F — Frequency**: how often do they buy? (more orders = better)
- **M — Monetary**: how much do they spend in total? (more spend = better)

Each customer gets a score of 1-4 on each dimension (4 = best, based on quartiles — i.e., the top 25% of customers on that dimension get a 4). The three scores combine into a segment label:

| Segment | Meaning | What a business should do |
|---|---|---|
| Champions | Bought recently, often, and spend a lot | Reward them, ask for referrals/reviews |
| Loyal Customers | Buy fairly often and recently | Upsell, keep engaged |
| New / Promising | Recent but low frequency so far | Nurture with onboarding offers |
| Needs Attention | Mid-range recency | Re-engagement campaigns |
| At Risk (High Value) | Haven't bought recently, but historically spent a lot | Win-back campaigns — highest priority, most money at stake |
| Lost / Churned | Haven't bought in a long time, low value | Low priority, or a "final offer" campaign |

**Where it lives in this project:** Precomputed in `data/raw/Customer_RFM.csv` (also in the Excel workbook) using Python/pandas, so it's ready to use immediately — no complex DAX required. It's included in Page 4 of the dashboard ("Customer Segmentation & Returns").

**How to talk about it in an interview:** "I used RFM segmentation to identify which customers are highest priority for retention — specifically the 'At Risk (High Value)' segment, since those customers have proven they'll spend a lot, but haven't purchased recently."

**If you want to build this yourself in DAX instead of Python** (optional stretch goal, more advanced): you'd use `CALCULATE` with `MAX(Fact_Sales[OrderDate])` per customer for recency, `DISTINCTCOUNT(Fact_Sales[OrderID])` for frequency, and quartile buckets via nested `SWITCH`/`IF` statements comparing each customer's score against `PERCENTILE.INC`. This is genuinely advanced DAX — the precomputed table is the practical, beginner-friendly path, but mentioning you understand the DAX alternative is a good interview talking point.

---

## 2. Sales Rep Leaderboard (Ranking)

**What it is:** Using the `RANKX` DAX function to rank every sales rep by total sales, generating a live leaderboard that updates automatically as filters/slicers change.

**Why it matters:** Ranking is one of the most commonly requested real-world features ("who are our top 5 salespeople this quarter?") and `RANKX` is one of the DAX functions interviewers most often ask about.

**Where it lives:** `Sales Rank` measure in `03_DAX_MEASURES.md`, used on Page 3 ("Sales Rep Performance").

---

## 3. Returns & Quality Analysis

**What it is:** A second fact table (`Fact_Returns`) tracking which orders were returned, why, and how much was refunded — separate from the main sales table, linked by `OrderID`.

**Why it matters:** Real businesses don't just track what sold — they track what came back, and why. This lets you calculate a **Return Rate %** and break down return reasons (damaged, wrong item, defective, etc.), which is exactly the kind of "loss analysis" a quality or operations team would ask a data analyst to build.

**Where it lives:** Page 4 of the dashboard, using the `Return Rate %`, `Total Refund Amount`, and `Net Sales After Returns` measures.

---

## 4. Built-in Forecasting (no DAX required)

Power BI's **Analytics pane** has a native forecasting feature for line charts — it fits a trend model to your historical data and projects it forward with a confidence interval, entirely through the UI.

**How to use it:** click your sales trend line chart → open the **Analytics** pane (magnifying-glass-with-a-line icon in Visualizations) → **Forecast** → **Add** → set how many periods to forecast → done.

**Why it's worth including:** it's a genuinely useful, low-effort feature that makes a dashboard feel more advanced, and it's a great thing to mention in an interview: "I used Power BI's built-in forecasting to project next quarter's sales trend."

---

## Suggested talking points for your resume / LinkedIn

> "Built an end-to-end Power BI sales analytics project on a synthetic dataset (6,000+ orders across 3 years), including a star-schema data model, DAX time-intelligence measures, RFM customer segmentation, a sales rep performance leaderboard, and a returns/quality analysis — published to GitHub with full documentation."

This single sentence signals data modeling, DAX, and business-analytics thinking all at once — exactly what recruiters scan for.
