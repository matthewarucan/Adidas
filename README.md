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
   ```
---
### 3.3 Add Date Fields

Add helper columns to enable time-based analysis.

1. Insert a **Year** column (assumes `Invoice Date` is in `C2`):
   ```excel
   =YEAR(C2)
   ```
2. Month (3-letter label):
    ```excel
    =TEXT(C2,"MMM")
    ```
3. Quarter:
    ```excel
    ="Q" & ROUNDUP(MONTH(C2)/3,0)
    ```
---
### 3.4 Basic Data Quality Checks
Revenue reconciliation:
    ```excel
    =ROUND([@[Price per Unit]]*[@[Units Sold]], 2) = [@[Total Sales]]
    ```
    - Filter for FALSE to find mismatches.

Invalid checks:
- Price per Unit <= 0
- Units Sold < 0
- Total Sales < 0

Missing keys:
- Filter blanks in Retailer, Region, State, City.

Duplicate check:
- Conditional Formatting → Duplicate Values.
---
### 3.5 Create Pivot Table for Channel Concentration
  

1. Insert Pivot: Insert → PivotTable → New Worksheet.

2.  Rows: Channel
    Values: Sum of Total Sales

3. Show as % of Grand Total.

4. Sort by % descending.

5. Cumulative % (assuming % of total in column B):
    ```excel
    =SUM($B$2:B2)
    ```

6. Highlight channels > 2%:
- Conditional Formatting → Cell Value > 0.02

Top-N quick metrics:
 ```excel

=B2           // Top-1
=SUM(B2:B6)   // Top-5
=SUM(B2:B11)  // Top-10

```














   
