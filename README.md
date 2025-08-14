![](assets/adidas_logo.png)
# Retail Channel Power Shift

## Step 1 — Understand & Define the Business Problem

### 1.1 Problem Statement
Adidas U.S. sales may be overly concentrated in a small number of **retailer–geography** combinations (e.g., `Retailer A – California – Los Angeles`).  
If one of these channels reduces shelf space, changes contract terms, or stops selling Adidas products, a **significant percentage of revenue is at risk**.

**Business Question:**  
> How concentrated is our revenue by channel (Retailer × Region × State × City), and what is the Revenue-at-Risk if a top channel underperforms or is lost?

---

### 1.2 Objectives & Outcomes
- **Measure concentration**: Top-N shares (Top-1/5/10) & Cumulative Share.
- **Identify critical channels**: Those with >2% of national revenue.
- **Quantify Revenue-at-Risk (RaR)**: Model the impact of losing a channel using a **Substitution Rate**.
- **Recommend actions**: Where to diversify and reduce dependency.

---

### 1.3 Stakeholders
- VP of Sales / Channel Managers
- Finance (risk assessment)
- Trade Marketing (channel growth plans)

---

### 1.4 Key Metrics
- **Top-N Revenue Share %**
- **Max Single Channel Share**
- **Revenue-at-Risk** (with substitution)
- **Target Concentration Level** for diversification

---

## Step 2 — Scope & Plan

### 2.1 Deliverables (Excel only)
- **Data** tab with cleaned dataset + added `Channel` column.
- **Pivot Tables** for Top-N channels & Cumulative Share.
- **Scenario Modeling** tab:
  - Dropdown to pick a channel.
  - Substitution % input cell.
  - Automatic RaR calculation.
- **Executive Summary** tab with:
  - KPIs, key visuals, recommendations.

---

### 2.2 Workflow Plan
| Phase   | Task                                          | Tool/Method                |
|---------|-----------------------------------------------|----------------------------|
| Setup   | Import data, clean headers, create `Channel`  | Excel formulas              |
| Analysis| Create Pivot Table for revenue share & cumulative % | Excel PivotTables      |
| Scenario| Build Revenue-at-Risk calculator              | Excel formulas & Data Validation |
| Summary | Build dashboard & recommendations             | Excel charts + summary sheet |

---

## Step 3 — Gather & Prepare Data (Excel Steps)

### 3.1 Load & Inspect
1. Open `Adidas US Sales Datasets.xlsx`.
2. Remove any empty rows at the top or bottom.
3. Check column headers — they should include:
   - `Retailer`, `Retailer ID`, `Invoice Date`, `Region`, `State`, `City`, `Product`, `Price per Unit`, `Units Sold`, `Total Sales`, `Operating Profit`, `Operating Margin`, `Sales Method`.

---

### 3.2 Create the `Channel` Column
**Goal:** Unique identifier = `Retailer - Region - State - City`.

Steps:
1. Insert a new column after `City`.
2. Name it `Channel`.
3. If:
   - `Retailer` = `A2`
   - `Region` = `D2`
   - `State` = `E2`
   - `City` = `F2`

   Use formula:
   ```excel
   =A2 & " - " & D2 & " - " & E2 & " - " & F2


---

## 3.3 Add Date Fields

Add helper columns to enable time-based analysis.

1. Insert a **Year** column (assumes `Invoice Date` is in `C2`):
   ```excel
   =YEAR(C2)


2. Insert a Month column (3‑letter text label):

=TEXT(C2,"MMM")


3. Insert a Quarter column:

="Q" & ROUNDUP(MONTH(C2)/3,0)

3.4 Basic Data Quality Checks

Revenue reconciliation (inside a helper column in the data table):

=ROUND([@[Price per Unit]]*[@[Units Sold]], 2) = [@[Total Sales]]


Filter this column for FALSE to find rows where price×units ≠ total.

Outlier/invalid checks (use Filters):

Price per Unit <= 0

Units Sold < 0

Total Sales < 0

Missing keys (Filter for blanks):

Retailer, Region, State, City

(Optional) Duplicate check: Create a helper key:

=[@[Retailer ID]] & "|" & TEXT([@[Invoice Date]],"yyyy-mm-dd") & "|" & [@[City]] & "|" & [@[Product]]


Then use Conditional Formatting → Duplicate Values on this column.

3.5 Create Pivot Table for Channel Concentration

Channel must already exist, e.g., =Retailer & " - " & Region & " - " & State & " - " & City.

Insert Pivot: Select the full table → Insert → PivotTable → New Worksheet.

Configure Fields:

Rows: Channel

Values: Sum of Total Sales

Show % of total:

In Values field settings → Show Values As → % of Grand Total.

Sort by % of Grand Total descending.

Add Cumulative % (in cells next to the pivot, e.g., column C, with % of total in B):

In C2:

=SUM($B$2:B2)


Fill down.

Highlight critical channels (> 2% share):

Select % of total column → Conditional Formatting → New Rule → Format only cells that contain → Cell Value > 0.02.

Top‑N quick metrics (assumes % of total is in B2:B1000 and sorted descending):

Top‑1:

=B2


Top‑5:

=SUM(B2:B6)


Top‑10:

=SUM(B2:B11)

3.6 Prepare for Scenario Modeling (Revenue‑at‑Risk)

Create a new sheet named Scenario.

Channel selector:

Create a list of channels (copy the pivot’s Channel labels to a hidden range, e.g., Scenario!A2:A500).

In a yellow input cell (e.g., Scenario!D2), apply Data → Data Validation → List pointing to that range.

Substitution Rate input (0–100%): put in Scenario!D3 (format as %).

Lookup the selected channel’s annual sales (assumes a two‑column table on Scenario sheet with Channel in A and Sales in B):

=XLOOKUP(D2, A:A, B:B, 0)


(If you don’t have XLOOKUP, use INDEX/MATCH):

=INDEX(B:B, MATCH(D2, A:A, 0))


Revenue‑at‑Risk (full loss):

= [Channel_Sales_Cell] * (1 - $D$3)


Underperformance scenario (e.g., −X% performance in D4 as %):

= [Channel_Sales_Cell] * $D$4 * (1 - $D$3)


(Optional) Multi‑channel scenario:

Sum several selected channels’ sales and apply the same formula.

Interpretation

Substitution Rate = portion of lost sales that “move” to other channels (e.g., 30%).

RaR = the part that is truly lost after substitution.

4. Analyze — Concentration, HHI, and Trends
4.1 Calculate HHI (Herfindahl–Hirschman Index)

HHI = sum of squared channel shares (shares must be decimal: 4% = 0.04).

Assume the pivot % of total is in B2:Bn. In an analysis cell:

=SUMPRODUCT(B2:Bn * B2:Bn)


Interpretation (rule of thumb):

< 0.15 → Unconcentrated

0.15–0.25 → Moderate concentration

> 0.25 → High concentration

Tip: If your % of total shows as 4%, Excel stores it as 0.04, so the formula above works directly.

4.2 Trend HHI by Quarter

Build a pivot with:

Rows: Channel

Columns: Quarter

Values: Sum of Total Sales

For each Quarter column, set Show Values As → % of Column Total (this gives share per channel within each quarter).

Compute HHI per Quarter (under each column, summing squared shares for that quarter), e.g., for quarter shares in E2:E200:

=SUMPRODUCT(E2:E200 * E2:E200)


Create a line chart with Quarter on X‑axis and HHI on Y‑axis to visualize concentration trends.

Interpretation

Rising HHI over time ⇒ dependency is increasing (riskier).

Falling HHI ⇒ revenue is more diversified.

4.3 Identify Critical Channels

Add a flag column next to your channel pivot:

=IF([@[Share %]]>=0.02,"CRITICAL","OK")


Filter to CRITICAL to list channels contributing ≥2% of national sales.

Create a Pareto view: sorted bars (share %) + cumulative line to see how fast total revenue accumulates.

Pareto Chart Steps

Copy Channel and % of total to a flat range (e.g., H2:I50).

In J2 (Cumulative %):

=SUM($I$2:I2)


Insert → Combo Chart:

Series1 = % of total → Clustered Column

Series2 = Cumulative % → Line (Secondary Axis)

4.4 (Optional) Volatility per Channel

Estimate variability (Month‑to‑Month % change) for the top channels.

Create a pivot:

Rows: Channel

Columns: Month (or Invoice Date grouped to months)

Values: Sum of Total Sales

Next to each row, compute STDEV.P across monthly sales:

=STDEV.P([Jan_Cell]:[Dec_Cell])


Use this to build a Risk Matrix (Share % on X, Volatility on Y).

5. Share — Executive Dashboard & Visuals (Excel)
5.1 KPI Tiles (cells with big fonts)

Place these in a Summary sheet:

Top‑1 Share (from B2 of your sorted list)

=B2


Top‑5 Share

=SUM(B2:B6)


Top‑10 Share

=SUM(B2:B11)


HHI

=SUMPRODUCT(B2:Bn*B2:Bn)


Format as Percentage for shares and Number (3 decimals) for HHI.

5.2 Charts

Pareto (Channel Share + Cumulative) — see 4.3 steps.

HHI Over Time — line chart of quarterly HHI (4.2).

Map Heat (optional, Excel 365):

Create a pivot with Rows: State, Values: Sum of Total Sales.

Insert → Map Chart (or use Conditional Formatting color scale on a state table).

5.3 Scenario Viewer

On the Scenario sheet:

Show:

Selected Channel

Channel Sales

Substitution Rate

Revenue‑at‑Risk (RaR)

Add a small bar or gauge:

Use a Data Bar conditional format on the RaR cell to make it visual.

Optional multi‑channel what‑if

Add checkboxes (Form Controls) to include/exclude several channels (sum their sales, apply the same RaR formula).

5.4 Executive Summary (copy/edit this block)

What we see: Revenue is concentrated in a small set of Retailer×Region×State×City channels; Top‑10 account for ~X% of sales; HHI = Y.YYY (moderate/high).

So what: If any critical channel (>2% share) underperforms, we face $Z RaR after accounting for substitution.

Now what:

Diversify in Regions A/B by growing Channels with similar customer profiles.

Protect top channels via co‑op marketing and service SLAs.

Test alternative retailers in states with high dependency.

(Replace X/Y/Z with your workbook’s outputs.)

6. Act — Recommendations, Targets, and Monitoring
6.1 Recommendations

De‑risk concentration

Target: Top‑10 Share ≤ 35% within 2 quarters.

Actions: Expand into 3–5 mid‑tier channels in the Midwest/South (low current share).

Protect critical channels

Quarterly Joint Business Plans, in‑season promo support, prioritized allocations.

Develop substitutes

Identify look‑alike channels (similar demographics/price points) for each critical channel and launch a pilot.





















   
