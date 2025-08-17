![Adidas Logo](assets/adidas_logo.png)

# Retail Channel Concentration & Revenue-at-Risk Analysis

> **Goal:** Measure channel concentration, quantify **Revenue-at-Risk (RaR)**, and recommend actions to reduce dependency on any single **Retailer × Region × State × City** combination.

## 1️⃣ Understand & Define

### 1.1 Problem Statement
Adidas U.S. sales may be overly concentrated in a small number of **retailer–geography** combinations (e.g., `Retailer A – California – Los Angeles`). If a top channel reduces shelf space, changes contract terms, or churns, a **meaningful share of revenue is at risk**.

**Business Question:**  
How concentrated is revenue by channel, and what is the **Revenue-at-Risk** if a top channel underperforms or is lost (with and without substitution)?

### 1.2 Objectives & Outcomes
- **Measure concentration:** Top-N shares (Top-1 / Top-5 / Top-10) and cumulative share.
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

## 2️⃣ Scope & Plan (Excel-Only Build)

### 2.1 Deliverables
- **Data Tab**: Cleaned dataset + computed channel field.
- **Channel_Summary Tab**: Channel shares, cumulative %, Top-N.
- **Scenario Tab**: Channel selector, Substitution %, RaR calculator.
- **KPI Tab**: KPI tiles, Pareto, HHI-over-time, recommendations.

### 2.2 Workflow

| Phase   | Task                                              | Tool/Method                    |
|--------:|---------------------------------------------------|--------------------------------|
| Setup   | Import & clean headers, create `Channel`          | Excel Table + formulas         |
| Analysis| Pivot: share %, cumulative %, Top-N               | PivotTables + % of Grand Total |
| Scenario| RaR calculator (selector + substitution)          | XLOOKUP + Data Validation      |
| Summary | KPIs + charts + exec summary                      | Charts + conditional formats   |

## 3️⃣ Data Preparation

### 3.1 Load & Inspect
1. Open `"Adidas US Sales Dataset.xlsx"`.
2. Remove empty rows at the top or bottom.
3. Ensure headers include:
   - Retailer, Retailer ID, Invoice Date, Region, State, City, Product, Price per Unit, Units Sold, Total Sales, Operating Profit, Operating Margin, Sales Method.

### 3.2 Create the 'Channel' Column
```excel
Channel = CONCAT([Retailer], "-", [Region], "-", [State], "-", [City])
```

### 3.3 Add Date Fields
```excel
Year = YEAR([Invoice Date])
Month = MONTH([Invoice Date])
Quarter = "Q" & ROUNDUP(MONTH([Invoice Date])/3,0)
```

### 3.4 Data Quality Checks
```excel
Revenue Reconciles = ROUND([Price per Unit]*[Units Sold],2) = [Total Sales]
```
- Filter FALSE → investigate mismatches.
- Check: Price per Unit > 0, Units Sold >= 0, Total Sales >= 0.

During reconciliation, ~50% of rows showed a 10× mismatch between Price × Units and Total Sales. This was traced to an error in the Units Sold column (values recorded at 1/10th of the true volume).

✅ Fix: Adjusted by scaling Units Sold by 10 where mismatches occurred. This correction ensures consistency in revenue calculations, prevents underestimation of sales volume, and keeps channel concentration and Revenue-at-Risk analysis accurate.









## 4️⃣ Excel Build Script

### 4.1 Channel Concentration
Pivot:
- Rows = Channel
- Values = Sum of Total Sales
- Show Values As → % of Grand Total
- Sort descending by share

Cumulative %:
```excel
=SUM($[% Total Sales]$2:[% Total Sales]2)
```

Top-N:
```excel
Top-1 = [% Total Sales]2
Top-5 = SUM([% Total Sales]2:[% Total Sales]6)
Top-10 = SUM([% Total Sales]2:[% Total Sales]11)
```

### 4.2 HHI (Herfindahl–Hirschman Index) by Quarter
Pivot:
- Rows = Channel
- Columns = Year, Quarter
- Values = Sum of Total Sales
- Show Values As → % of Column Total

HHI (e.g., C2:C200 are shares for Q1 2020):
```excel
=SUMPRODUCT(C$2:C$200*C$2:C$200)
```

Interpretation (cell with HHI in C201):
```excel
=IF(C201<0.15,"Low",IF(C201<=0.25,"Moderate","High"))
```

### 4.3 Critical Channels
```excel
=IF([@[Share %]]>=0.02,"CRITICAL","OK")
```






















## Section 5 TITLE

### 5.1 Scenario Modeling (Revenue-at-Risk)
Scenario!D2 → Channel selector (Data Validation from Channel_List)  
Scenario!D3 → Substitution Rate (e.g., 0.50 for 50%)

Channel Sales Lookup:
```excel
=XLOOKUP($D$2, Channel_Summary!$A:$A, Channel_Summary!$C:$C, 0)
```

Revenue-at-Risk:
```excel
=$D$5*(1-$D$3)
```
## Section 6 TITLE

### 6.1 KPI Tiles
Top-1 Share:
```excel
=MAX(Channel_Summary!E:E)
```
Top-5 Share:
```excel
=SUM(Channel_Summary!E2:E6)
```

Top-10 Share:
```excel
=SUM(Channel_Summary!E2:E11)
```

HHI Latest:
[link to latest quarter HHI cell]

### 6.2 Pareto Cumulative %
```excel
=SUM($E$2:E2)
```

### 6.3 Charts
- Pareto: Column (share) + Line (cumulative %)
- HHI Trend: Line chart over quarters
- RaR Scenario: Data bars on RaR

### 7. Executive Summary
What:  
2020 Q1 HHI = 0.445 (High), Top-10 Share ~70% → significant dependency risk.

So What:  
Losing a top channel could remove >$X M in quarterly sales.  
Substitution rate scenarios show only partial recovery.

Now What:  
- Reduce Top-10 share to ≤35% within 12 months.  
- Add mid-tier channels to diversify revenue base.  
- Increase protection measures for CRITICAL channels (>2% share).

### 8. Monitoring
- Keep Data tab in Table format  
- Refresh pivots when updating data  
- Scenario + KPI tiles auto-update  
- Track HHI trend quarterly to spot concentration creep












