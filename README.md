# 🛒 Blinkit Sales Analysis — Power BI Dashboard

A comprehensive end-to-end Power BI project analyzing **Blinkit's sales performance, customer satisfaction, and inventory distribution across outlet types, locations, and item categories.

## 📌 Table of Contents

- [Project Overview](#-project-overview)
- [Business Requirements & KPIs](#-business-requirements--kpis)
- [Dataset Description](#-dataset-description)
- [Project Workflow](#-project-workflow)
- [Data Cleaning Steps](#-data-cleaning-steps-power-query-editor)
- [DAX Measures](#-dax-measures)
- [Dashboard Visualizations](#-dashboard-visualizations)
- [Key Findings & Insights](#-key-findings--insights)
- [Dashboard Preview](#-dashboard-preview)
  

## 📋 Project Overview

| Field | Detail |
| **Tool** | Microsoft Power BI Desktop |
| **Dataset** | Blinkit Grocery Data |
| **Domain** | Retail / Quick Commerce |
| **Objective** | Analyze sales performance, customer satisfaction, and inventory distribution to surface actionable business insights |

## 🎯 Business Requirements & KPIs

### Project Objective
Conduct a comprehensive analysis of Blinkit's sales performance, customer satisfaction, and inventory distribution to identify key insights using Power BI dashboards.

### Key Performance Indicators (KPIs)

| KPI | Description | Dashboard Value |
|---|---|---|
| **Total Sales** | Overall revenue generated from all items sold | ₹219.87K |
| **Average Sales** | Average revenue per transaction | ₹141.03 |
| **Number of Items Sold** | Total count of distinct items sold | 2,000 |
| **Average Rating** | Average customer rating across all items | 4.0 |

## 📂 Dataset Description

The dataset contains grocery sales records from Blinkit outlets across India.

| Column | Description |
|---|---|
| `Item Identifier` | Unique product ID |
| `Item Type` | Category (Snacks, Fruits, Household, etc.) |
| `Item Fat Content` | Low Fat / Regular |
| `Item Weight` | Weight of product |
| `Item Visibility` | Display visibility score |
| `Item MRP` | Maximum Retail Price |
| `Outlet Identifier` | Unique outlet ID |
| `Outlet Establishment Year` | Year the outlet was established |
| `Outlet Size` | Small / Medium / High |
| `Outlet Location Type` | Tier 1 / Tier 2 / Tier 3 |
| `Outlet Type` | Grocery Store / Supermarket Type 1/2/3 |
| `Item Outlet Sales` | Target variable — actual sales |


## 🧹 Data Cleaning Steps (Power Query Editor)

All transformations were performed inside **Power Query Editor** before loading data into the model.

### Step 1 — Promoted Headers
- Ensured the first row was correctly promoted as column headers.

### Step 2 — Data Type Corrections
```
Item Weight          → Decimal Number
Item MRP             → Decimal Number
Item Outlet Sales    → Decimal Number
Outlet Establishment Year → Whole Number
Item Visibility      → Decimal Number
```

### Step 3 — Fat Content Standardization ⚠️ (Critical Fix)
The `Item Fat Content` column had **inconsistent values** representing the same categories:

| Raw Values Found | Standardized To |
|---|---|
| `LF`, `low fat` | `Low Fat` |
| `reg` | `Regular` |

**Power Query M Code used:**
```m
= Table.ReplaceValue(
    Table.ReplaceValue(#"Previous Step",
        "LF", "Low Fat", Replacer.ReplaceValue, {"Item Fat Content"}),
    "reg", "Regular", Replacer.ReplaceValue, {"Item Fat Content"})
```

> Without this fix, the Fat Content donut chart would have shown 4–5 slices instead of 2, making analysis misleading.

### Step 4 — Null Value Handling
- `Item Weight`: Checked for nulls — retained rows since weight was not used as a primary dimension in any KPI calculation.
- `Outlet Size`: No nulls found after type correction.

### Step 5 — Removed Duplicates
- Verified no duplicate `Item Identifier` + `Outlet Identifier` combinations existed.

### Step 6 — Column Profiling Check
Used **View → Column Quality / Column Distribution / Column Profile** to validate:
- `Item Fat Content`: Confirmed only 2 distinct values after cleaning
- `Outlet Type`: Confirmed 4 distinct outlet types
- `Outlet Location Type`: Confirmed exactly Tier 1, Tier 2, Tier 3


## 📊 Dashboard Visualizations

| # | Chart Title | Chart Type | Key Dimension |
|---|---|---|---|
| 1 | Total Sales by Fat Content | Donut Chart | Item Fat Content |
| 2 | Total Sales by Item Type | Horizontal Bar Chart | Item Type |
| 3 | Fat Content by Outlet for Total Sales | Stacked Bar Chart | Outlet Location × Fat Content |
| 4 | Total Sales by Outlet Establishment Year | Line Chart | Year |
| 5 | Sales by Outlet Size | Pie Chart | Outlet Size |
| 6 | Sales by Outlet Location Type | Bar Chart | Tier 1 / 2 / 3 |
| 7 | All Metrics by Outlet Type | Matrix Table | Outlet Type |

### Filter Panel (Slicers)
Three interactive slicers allow cross-filtering all visuals simultaneously:
- **Outlet Location Type** (Tier 1 / 2 / 3)
- **Item Type** (16 categories)
- **Item Fat Content** (Low Fat / Regular)

## 💡 Key Findings & Insights

### 1. Tier 3 Cities Outperform Tier 1 — Counterintuitive Result
> Tier 3 cities generated **₹78.16K** in sales vs Tier 1's **₹72.36K**, despite Tier 1 typically having higher purchasing power.

**Hypothesis:** Blinkit likely has more outlet density in Tier 3 cities, or Tier 3 consumers depend more heavily on quick-commerce for essential groceries due to fewer retail alternatives.

---

### 2. Supermarket Type 1 Dominates — 67% of Total Revenue
> Supermarket Type 1 alone accounts for **₹1,48,052** out of ₹2,19,870 total sales (~67%).

**Implication:** This outlet type is the core revenue engine. Any operational issues (stockouts, delivery delays) at Type 1 supermarkets would disproportionately impact overall revenue.

---

### 3. Medium Outlets Beat High-Capacity Outlets
> Medium-sized outlets hold **42.55%** of total sales vs High outlets at 37.01%.

**Implication:** Scale is not directly correlated with revenue here. Medium outlets may benefit from better location placement or optimized SKU selection. This warrants an outlet-level profitability analysis.

---

### 4. Snack Foods & Fruits Tied at ₹32K Despite Very Different Logistics
> Both categories generate equal revenue, but Fruits are perishable and require cold chain management.

**Implication:** Fruits delivering equivalent revenue to shelf-stable Snacks suggests Blinkit's quick-delivery model effectively neutralizes perishability risk — a genuine operational strength.

---

### 5. Sales Peak Around 2011–2012, Then Flatten
> The outlet establishment line chart shows a spike in 2011–2012 followed by a sharp decline, then gradual recovery.

**Context:** This aligns with Grofers' (now Blinkit) early rapid expansion phase, followed by a contraction as the business rationalized underperforming outlets before its pivot to quick commerce (10-minute delivery) model post-2020.

---

### 6. Average Rating Variance is Near-Zero Across All Outlet Types
> Ratings range only from **3.9 to 4.1** across all outlet types — a suspiciously flat distribution.

**Caution flag:** This low variance may indicate the rating data is synthetic or averaged at an aggregated level, which would make it unreliable as a customer satisfaction signal. In a real business context, this KPI would need validation against raw review-level data.

---

### 7. Regular Fat Content Outsells Low Fat (64.75% vs 35.25%)
> Despite health-consciousness trends, Regular fat products account for nearly two-thirds of sales.

**Implication:** Blinkit's customer base is primarily buying for convenience and taste, not health optimization. Marketing strategies pushing "healthy" SKUs would need behavioral nudges, not just catalog expansion.


### Files in this Repository
```
📁 blinkit-sales-analysis/
├── 📊 BlinkIT Sales Analysis.pbix     ← Main Power BI file
├── 📄 BlinkIT Grocery Data.xlsx       ← Raw dataset
├── 📸 dashboard_screenshot.png        ← Dashboard preview image
└── 📝 README.md                       ← This file
```

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| Power BI Desktop | Dashboard development & visualization |
| Power Query Editor | Data cleaning & transformation |
| Excel (.xlsx) | Source data format |

Project by Shruti Nair
