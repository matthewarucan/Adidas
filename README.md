![Adidas Logo](assets/adidas_logo.png)

# Retail Channel Power Shift — Excel Playbook

> Measure concentration, quantify **Revenue-at-Risk (RaR)**, and recommend actions to reduce dependency on any single **Retailer × Region × State × City** channel.

---

## 1) Understand & Define

### 1.1 Problem Statement
Adidas U.S. sales may be overly concentrated in a small number of **retailer–geography** combinations (e.g., `Retailer A – California – Los Angeles`). If a top channel reduces shelf space, changes terms, or churns, a **meaningful share of revenue is at risk**.

**Business Question:**  
How concentrated is revenue by channel, and what is the **Revenue-at-Risk** if a top channel underperforms or is lost (with and without substitution)?

### 1.2 Objectives & Outcomes
- **Measure concentration:** Top-N shares (Top-1/5/10) and cumulative share.
- **Identify critical channels:** Threshold > **2%** of national revenue.
- **Quantify RaR:** Apply a configurable **Substitution Rate**.
- **Recommend actions:** Diversify, protect, and grow resilient channels.

### 1.3 Stakeholders
- VP of Sales & Channel Managers  
- Finance (Risk & Scenario)  
- Trade Marketing (Growth Planning)

### 1.4 Key Metrics (KPIs)
- **Max Single-Channel Share %**
- **Top-N Revenue Share % (Top-1/5/10)**
- **Revenue-at-Risk ($ and %)**
- **HHI (Herfindahl–Hirschman Index)**
- **Target Concentration Level** (e.g., Top-10 ≤ 35%)

---

## 2) Scope & Plan (Excel-Only Build)

### 2.1 Deliverables
- **Data** tab: Cleaned dataset + computed **Channel** field.
- **Pivots** tab: Channel shares, cumulative %, Top-N.
- **Scenario** tab: Channel selector, Substitution %, **RaR** calculator.
- **Summary** tab: KPI tiles, Pareto, HHI-over-time, recommendations.

### 2.2 Workflow
| Phase   | Task                                              | Tool/Method                    |
|--------:|---------------------------------------------------|--------------------------------|
| Setup   | Import & clean headers, create `Channel`          | Excel Table + formulas         |
| Analysis| Pivot: share %, cumulative %, Top-N               | PivotTables + % of Grand Total |
| Scenario| RaR calculator (selector + substitution)          | XLOOKUP + Data Validation      |
| Summary | KPIs + charts + exec summary                      | Charts + conditional formats   |

---

## 3–6) Excel Build Script

```excel
// 3.1 Load & Inspect
1. Open "Adidas US Sales Datasets.xlsx"
2. Remove any empty rows at the top or bottom.
3. Ensure headers include: Retailer, Retailer ID, Invoice Date, Region, State, City, Product, Price per Unit, Units Sold, Total Sales, Operating Profit, Operating Margin, Sales Method.

// 3.2 Create the 'Channel' Column
Goal: Unique key = Retailer - Region - State - City
=A2 & " - " & D2 & " - " & E2 & " - " & F2

// 3.3 Add Date Fields (C2 = Invoice Date)
=YEAR(C2)                              // Year
=TEXT(C2,"MMM")                        // Month label
="Q" & ROUNDUP(MONTH(C2)/3,0)          // Quarter

// 3.4 Data Quality Checks
=ROUND([@[Price per Unit]]*[@[Units Sold]],2)=[@[Total Sales]]
// Filter FALSE for mismatches
// Invalid filters: Price per Unit <= 0, Units Sold < 0, Total Sales < 0
// Missing keys: blanks in Retailer, Region, State, City
// Duplicates: Conditional Formatting → Duplicate Values

// 3.5 Pivot Table for Channel Concentration
Rows: Channel
Values: Sum of Total Sales
Show Values As: % of Grand Total
Sort: Descending by %
Cumulative % (B2 = % of total):
=SUM($B$2:B2)
Highlight > 0.02:
Conditional Formatting → Greater Than 0.02
Top-N:
=B2                 // Top-1
=SUM(B2:B6)         // Top-5
=SUM(B2:B11)        // Top-10

// 3.6 Scenario Modeling (Revenue-at-Risk)
Scenario!D2 → Channel selector (Data Validation)
Scenario!D3 → Substitution Rate
Channel sales lookup:
=XLOOKUP(D2, A:A, B:B, 0)
Revenue-at-Risk:
=[Channel_Sales_Cell] * (1 - $D$3)

// 4.1 HHI (Herfindahl–Hirschman Index)
=SUMPRODUCT(B2:Bn * B2:Bn)
Interpretation:
< 0.15 → Low
0.15–0.25 → Moderate
> 0.25 → High

// 4.2 Trend HHI by Quarter
Pivot: Rows = Channel, Cols = Quarter, Values = Total Sales
Show Values As: % of Column Total
Per quarter:
=SUMPRODUCT(E2:E200 * E2:E200)

// 4.3 Critical Channels
=IF([@[Share %]]>=0.02,"CRITICAL","OK")

// 4.4 Pareto Cumulative %
=SUM($I$2:I2)

// 5.1 KPI Tiles
=B2                      // Top-1 share
=SUM(B2:B6)              // Top-5 share
=SUM(B2:B11)             // Top-10 share
=SUMPRODUCT(B2:Bn*B2:Bn) // HHI

// 5.2 Charts
Pareto: share + cumulative %
HHI over time: line chart
Map heat: optional if geo present

// 5.3 Scenario Viewer
Show: Channel, Sales, Substitution Rate, RaR
Use data bars on RaR

// 5.4 Executive Summary
What: Concentration level + HHI
So What: Risk & $ impact
Now What: Diversify, protect, test alternatives

// 6.1 Recommendations
Reduce Top-10 ≤ 35%
Add mid-tier channels
Protect criticals

// 6.2 Monitoring
Keep data in Table format
Refresh pivots on update
Scenario + KPIs auto-update
