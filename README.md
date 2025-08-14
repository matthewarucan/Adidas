![](assets/adidas_logo.png)
# Retail Price Elasticity & Profit Optimization for Adidas Sales Channels

# Retail Channel Power Shift — Excel-Only Business Analyst Project

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
| Phase | Task | Tool/Method |
|-------|------|-------------|
| Setup | Import data, clean headers, create `Channel` column | Excel formulas |
| Analysis | Create Pivot Table for revenue share & cumulative % | Excel PivotTables |
| Scenario | Build Revenue-at-Risk calculator | Excel formulas & Data Validation |
| Summary | Build dashboard & recommendations | Excel charts + summary sheet |

---

## Step 3 — Gather & Prepare Data (Excel Steps)

### 3.1 Load & Inspect
1. Open `Adidas US Sales Datasets.xlsx`.
2. Remove any empty rows at top or bottom.
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
