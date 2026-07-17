# 🧾 Vendor Invoice Intelligence System

A data analysis and intelligence system for processing, cleaning, and analyzing vendor invoice data for a retail liquor distribution business. The system ingests raw transactional data from multiple sources, stores it in a SQLite database, and produces actionable insights on vendor performance, invoice patterns, and profitability.

---

## Why this project

Businesses that purchase from multiple vendors and sell across multiple stores generate large volumes of transactional data — but raw transactions don't answer business questions on their own. This project builds the full pipeline from messy, inconsistent raw data to clean structured data to business KPIs to predictive models, the way a real data analytics team would approach it.

## Problem Statement

Using 7 related datasets covering purchases, sales, inventory, and vendor invoices, this project answers:
- Which vendors and brands drive the most profit — and which underperform?
- Where is inventory turning slowly or sitting as dead stock?
- Are purchase volumes aligned with actual sales demand?
- Can invoice risk/anomaly patterns be identified from billing data?
- Can future sales and inventory needs be forecasted?

---

## 📁 Project Structure

```
Vendor-Invoice-Intelligence-System/
│
├── data/
│   ├── raw/                        # Original unmodified source CSVs
│   └── processed/                  # Cleaned CSVs after data cleaning pipeline
│
├── database/
│   └── vendor_invoice.db           # SQLite database with all cleaned tables
│
├── models/                         # ML models and scoring outputs (future)
│
├── notebooks/
│   ├── 01_data_understanding.ipynb # EDA and dataset profiling
│   ├── 02_data_cleaning.ipynb      # Data cleaning, type fixes, and DB loading
│   ├── 03_eda.ipynb                # Exploratory analysis and visual insights (upcoming)
│   ├── 04_feature_engineering.ipynb
│   ├── 05_business_kpis.ipynb
│   ├── 06_ml_sales_prediction.ipynb
│   ├── 07_ml_inventory_forecasting.ipynb
│   ├── 08_ml_vendor_performance.ipynb
│   └── 09_ml_invoice_risk_fraud.ipynb
│
├── src/
│   └── data/
│       └── load_data.py            # Utility script for loading raw data (future)
│
├── dashboard/                      # Streamlit dashboard (future)
├── reports/figures/                # Saved charts and visual outputs (future)
│
├── requirements.txt                # Python dependencies
└── README.md
```

---

## 📊 Datasets

Seven related CSV files, joined primarily on `VendorNumber`, `InventoryId`, `Brand`, `Store`, and `PONumber`:

| File | Description |
|---|---|
| `begin_inventory.csv` | Inventory levels at the start of the period |
| `end_inventory.csv` | Inventory levels at the end of the period |
| `purchase_prices.csv` | Reference pricing and cost data by brand/vendor |
| `purchases.csv` | Line-item level purchase order transactions |
| `sales.csv` | Line-item level retail sales transactions (12.8M+ rows) |
| `vendor_invoice.csv` | Invoice-level vendor billing and freight data |
| `vendor_sales_summary.csv` | Pre-aggregated reference summary — used only to validate independently-built KPIs, not as a primary data source |

---

## 🗄️ Database Schema

All cleaned data is stored in a SQLite database (`database/vendor_invoice.db`) with the following tables:

- `begin_inventory`
- `end_inventory`
- `purchase_prices`
- `purchases`
- `sales`
- `vendor_invoice`
- `vendor_sales_summary`

---

## 🔬 Notebook Pipeline

### `01_data_understanding.ipynb`
- Loads all 7 raw datasets
- Profiles each dataset: shape, columns, dtypes, missing values, duplicates
- Identifies data quality issues for cleaning

### `02_data_cleaning.ipynb`
- **Column standardization**: Renames `VendorNo` → `VendorNumber` in sales for consistency
- **Date type conversion**: Parses all date columns from string to `datetime64`
- **Volume cleaning**: Converts `Volume` from string to numeric; drops rows with `Unknown` volume
- **Missing value handling**: Fills missing `City` in `end_inventory` using `begin_inventory` lookup
- **Approval flag engineering**: Converts `Approval` text column to binary `Approval_Flag` (0/1)
- **Outlier review**: Inspects extreme values in `Quantity` and `Dollars`
- **Saves** cleaned data to:
  - `data/processed/` as CSVs
  - `database/vendor_invoice.db` as SQLite tables

### `03_eda.ipynb`
Ten-section exploratory analysis covering:
- Purchase vs. Sales overview and overall profitability
- Top/bottom vendor and brand profitability
- Dead stock analysis (by unit count and by dollar value tied up)
- Monthly sales trend and seasonality
- Price and profit margin distributions (with outlier handling via percentile trimming)
- Freight cost and Approval Flag behavior across vendors
- Correlation analysis across core business metrics
- Consolidated key findings summary with implications for later phases

**Key insight**: A consistent group of premium, slow-moving products (e.g., Ch Haut Brion 10,
Kilbeggan Irish Whiskey) recurs across brand profitability, dead stock, and margin analyses —
$3.46M in dead stock value is concentrated in high-price, low-volume items, while 178 products
show zero recorded sales despite real purchase cost.

### `04_feature_engineering.ipynb`
Builds five reusable features on top of the cleaned data:
- **Time-based features**: Month and Quarter extracted from sales dates to make seasonality explicit
- **PaymentDelayDays**: Days between invoice and payment (avg. ~35 days, tightly consistent)
- **FreightRatio**: Freight as a % of invoice value — surfaces small, proportionally expensive
  orders independent of the Approval_Flag (which reflects absolute invoice size instead)
- **IsPremiumSlowMoving**: A validated, data-driven flag (75th percentile price + 25th percentile
  sales quantity) identifying the premium/low-volume segment recurring throughout EDA — flagged
  products average -$303 GrossProfit vs. +$13,489 for the rest of the catalog
- **MarginCategory**: Bucketed ProfitMargin (Negative / Low / Medium / High / No Sales) — reveals
  that ~20% of the catalog is either losing money or has zero recorded sales

---


## ⚙️ Setup

```bash
# Clone the repository
git clone https://github.com/aditibarmaiya-ds/vendor-invoice-intelligence-system.git
cd Vendor-Invoice-Intelligence-System

# Create and activate a virtual environment
python -m venv venv

# Windows
venv\Scripts\activate

# macOS/Linux
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Launch Jupyter and run notebooks in order
jupyter notebook
```
1. `notebooks/01_data_understanding.ipynb`
2. `notebooks/02_data_cleaning.ipynb`

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Python 3.13 | Core language |
| pandas, numpy | Data loading, cleaning, transformation |
| sqlite3 | Lightweight relational database for large-scale querying |
| matplotlib | Visualization (upcoming) |
| scikit-learn | Machine learning models (upcoming) |
| Streamlit | Interactive dashboard (upcoming) |
| Jupyter Notebook | Interactive EDA and pipeline |

---

## 🚧 Project Status

- [x] **Phase 1 — Data Understanding**: Full schema, dtype, null, and shape profiling across all 7 datasets
- [x] **Phase 2 — Data Cleaning**: Type fixes, missing value resolution, outlier review, standardization, loaded into SQLite
- [x] **Phase 3 — Exploratory Data Analysis**: 10-section analysis covering profitability, dead stock, seasonality, pricing, and correlations
- [x] **Phase 4 — Feature Engineering**: Time features, payment delay, freight ratio, validated premium/slow-moving flag, margin categories
- [ ] **Phase 5 — Business KPI Creation**
- [ ] **Phase 6 — Machine Learning Models** (Sales Prediction, Inventory Forecasting, Vendor Performance, Invoice Risk Detection)
- [ ] **Phase 7 — Streamlit Dashboard**
- [ ] **Phase 8 — Documentation**

---

## 👤 Author

**Aditi Barmaiya**
