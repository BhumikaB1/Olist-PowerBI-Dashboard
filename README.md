# E-Commerce Revenue Intelligence Dashboard
### Power BI | DAX | Power Query | Star Schema | Row-Level Security

## Project Overview

End-to-end business intelligence dashboard built on 100,000+ real
e-commerce transactions from the Olist Brazilian E-Commerce dataset
(Kaggle). Covers the full analytics stack: data cleaning, modelling,
DAX measures, dashboard design, and enterprise features like RLS.

**Business questions answered:**
- Which states and product categories drive the most revenue?
- What does the customer acquisition trend look like over time?
- How healthy is customer retention, and where is the gap?
- What is the average customer lifetime value?

---

## Key Insights Found

| Insight | Finding |
|---|---|
| Top revenue state | SP drives R$5.9M — 37% of total |
| Geographic concentration | SP + RJ = 55%+ of all orders |
| Product Pareto | Top 3 categories = ~45% of revenue |
| Retention gap | Repeat rate only 3.01% |
| Peak acquisition | November 2017 (Black Friday effect) |
| Avg order value | R$161.33 across 98K orders |

---

## Dashboard Pages

**Page 1 — Executive Summary**
KPI cards (Revenue, Orders, AOV, Repeat Rate) + Revenue trend
over time + Top states by revenue bar chart + Year slicer

**Page 2 — Product Analysis**
Revenue by category bar chart + Category matrix (Revenue, Orders,
AOV) + Revenue share treemap + Pareto insight annotation

**Page 3 — Customer Insights**
Monthly customer acquisition bar chart + Repeat vs Total customers
donut + Customer LTV over time + Retention insight callout

---

## Data Model (Star Schema)

```
Dim_Date ──────┐
Dim_Customers ─┤
               ├── Fact_Orders ── Fact_OrderItems ── Dim_Products
Dim_Payments ──┘
```

5 tables, all relationships set with correct cardinality (1:M).
Foreign key columns hidden from report view.

---

## DAX Measures(Some of them)

All measures stored in a dedicated _Measures table.

**Total Revenue**
```
Total Revenue = SUMX(Fact_OrderItems, [price] + [freight_value])
```

**MoM Revenue Growth %**
```
MoM Growth % =
DIVIDE(
  [Total Revenue] - CALCULATE([Total Revenue],
    DATEADD(Dim_Date[Date], -1, MONTH)),
  CALCULATE([Total Revenue],
    DATEADD(Dim_Date[Date], -1, MONTH)),
  0
)
```

**Rolling 3-Month Average Revenue**
```
Rolling 3M Avg Revenue =
CALCULATE(
  [Total Revenue],
  DATESINPERIOD(Dim_Date[Date],
    LASTDATE(Dim_Date[Date]), -3, MONTH)
) / 3
```

**Customer Lifetime Value**
```
Customer LTV =
AVERAGEX(
  VALUES(Dim_Customers[customer_id]),
  CALCULATE([Total Revenue])
)
```

**Repeat Rate %**
```
Repeat Rate % =
DIVIDE(
  COUNTROWS(FILTER(VALUES(Dim_Customers[customer_id]),
    CALCULATE([Total Orders]) > 1)),
  [Total Customers]
)
```

**Revenue YTD**
```
Revenue YTD = TOTALYTD([Total Revenue], Dim_Date[Date])
```

---

## Technical Highlights

- **Power Query (M):** Date type standardisation, null handling,
  Portuguese → English category name translation, custom Date
  dimension table generated via M code
- **Row-Level Security:** Regional role filter built and tested
  using 'View As Role' in Power BI Desktop
- **Date Intelligence:** Proper Dim_Date table with Year, Month,
  MonthName, Quarter columns — enables all time intelligence DAX

---

## Dataset

**Olist Brazilian E-Commerce Public Dataset**
Source: [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)
Size: ~100,000 orders | 2016–2018 | 5 CSV files used

---

## Files in This Repo

| File | Description |
|---|---|
| `Olist_Ecommerce_Dashboard.pbix` | Full Power BI file |
| `Dashboard PDF` |  all 3 dashboard pages |
| `README.md` | This file |

---

## Author

**Bhumika Patrange**
B.Tech CSE — D Y Patil International University, Pune
[LinkedIn](https://www.linkedin.com/in/bhumika-patrange
