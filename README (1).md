# Power BI Projects 📊

This repository contains Power BI dashboards I have built using real-world datasets. Each project demonstrates end-to-end report development — from data modelling and DAX measure creation through to insight delivery and visual design.

---

## Projects

### 1. CRM Sales Opportunities Analysis

**Dataset:** Maven Analytics — CRM Sales Opportunities  
**Tool:** Power BI Desktop  
**Pages:** 2

---

#### Overview

This report analyses a B2B CRM sales pipeline to uncover win rate trends, revenue performance, product effectiveness, and individual sales manager contribution. The goal was to give a sales leadership team a single source of truth for monitoring pipeline health and identifying where deals are being won or lost.

---

### Page 1 — Pipeline Performance Overview

#### Key Insights

- Overall win rate is **63.15%** across the full pipeline with **4,238 won deals** generating **$10.01M in total revenue**

- Win rate **declined from 82% in Q1 2017 to 60.25% by Q4 2017** — a clear downward trend that signals a need for pipeline review

- **Melvin Marxen** leads all sales managers with **882 won deals**, followed closely by Summer Sewald at 828

- **Mg Special** has the highest win rate by product at **64.84%**, outperforming all other product lines

- Regional filters allow leadership to slice performance by **Central, East and West** office to compare regional contribution

#### Visuals Used

- **KPI Cards** — Won Deals, Total Revenue, Win Rate %
- **Line Chart** — Win Rate % trend by Year-Quarter
- **Bar Chart** — Won Deals by Sales Manager
- **Bar Chart** — Win Rate % by Product
- **Pie Chart** — Deal stage distribution (Won, Lost, Engaging, Prospecting)
- **Slicers** — Regional Office, Year-Quarter

---

### Page 2 — Sales Agent Performance Breakdown

#### Why I Built This Page

Page 1 shows overall pipeline trends — but averages can be misleading. A sales agent with a **60% win rate** may be generating significantly more revenue than an agent with an **80% win rate**, simply because they are closing higher-value deals.

Relying on win rate alone to evaluate performance gives an incomplete picture. This page was built to show both metrics side by side — so leadership can make fairer, more informed decisions about agent performance rather than ranking people on a single number.

#### Key Insights

- **Darcel Schlecht** generates the highest total revenue at **$773,129** despite not having the highest win rate — confirming that win rate alone does not tell the full story

- **Maureen Marcano** achieves an **81.25% win rate** but lower total revenue — suggesting she closes more deals at lower values

- **Rosalina Dieter** stands out with an **83.33% win rate** — the highest in the table — making her the most consistent closer by percentage

- **Gtxpro** appears as the most common top product across high-revenue agents, suggesting it is the most commercially significant product in the portfolio

#### Visuals Used

- **Table visual** — Sales Agent, Total Revenue, Win Rate %, Top Product
- Sorted by Total Revenue descending to surface highest value agents first

---

#### Data Model

The report is built across 5 related tables:

| Table | Description |
|---|---|
| `sales_pipeline` | Core fact table — deal stages, close values, close dates |
| `accounts` | Customer and account dimension |
| `products` | Product dimension with pricing |
| `sales_teams` | Sales manager and team dimension |
| `Date Table` | Custom date table built in DAX |

---

#### DAX Measures & Calculations

**Custom Date Table** — built from scratch to enable time intelligence functions:

```dax
Date Table = 
ADDCOLUMNS(
    CALENDAR(
        MIN(sales_pipeline[close_date]),
        MAX(sales_pipeline[close_date])
    ),
    "Year",         YEAR([Date]),
    "Quarter",      "Q" & QUARTER([Date]),
    "Year-Quarter", "Q" & QUARTER([Date]) & " " & YEAR([Date]),
    "Month",        MONTH([Date]),
    "Month Name",   FORMAT([Date], "MMM")
)
```

> Creating a proper date table is essential for time intelligence in Power BI. Without it, functions like PREVIOUSQUARTER() will not work correctly.

---

**Total Revenue** — filters the pipeline to Won deals only:

```dax
Total Revenue = 
CALCULATE(
    SUM(sales_pipeline[close_value]),
    sales_pipeline[deal_stage] = "Won"
)
```

---

**Win Rate %** — divides won deals by total closed deals:

```dax
Win Rate% = 
DIVIDE(
    [Won Deals],
    [Won Deals] + [Lost Deals],
    0
)
```

> DIVIDE() is used instead of / to safely handle division by zero — returning 0 instead of an error.

---

**QoQ Revenue Growth %** — calculates revenue change vs the previous quarter:

```dax
QoQ Revenue Growth % = 
VAR CurrentQ  = [Total Revenue]
VAR PreviousQ = 
    CALCULATE(
        [Total Revenue],
        PREVIOUSQUARTER('Date Table'[Date])
    )
RETURN
    DIVIDE(
        CurrentQ - PreviousQ,
        PreviousQ,
        0
    )
```

> This measure uses VAR to store intermediate results making the logic easier to read and debug. PREVIOUSQUARTER() requires a properly marked date table to function correctly.

---

#### Files

| File | Description |
|---|---|
| `CRM_Sales_Opportunities.pbix` | Power BI report file |
| `CRM_Sales_Opportunities.pdf` | PDF export of the report |

---

*Dataset source: [Maven Analytics](https://mavenanalytics.io)*
