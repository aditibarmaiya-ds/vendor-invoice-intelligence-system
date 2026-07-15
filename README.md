# 🧾 Vendor Invoice Intelligence System

A data analysis and intelligence system for processing, cleaning, and analyzing vendor invoice data for a retail liquor distribution business. The system ingests raw transactional data from multiple sources, stores it in a SQLite database, and produces actionable insights on vendor performance, invoice patterns, and profitability.

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
│   └── 02_data_cleaning.ipynb      # Data cleaning, type fixes, and DB loading
│
├── src/
│   └── data/
│       └── load_data.py            # Utility script for loading raw data
│
├── dashboard/                      # Visualization dashboard (future)
│
├── requirements.txt                # Python dependencies
└── README.md
```

---

## 📊 Datasets

| File | Rows | Description |
|---|---|---|
| `begin_inventory.csv` | 206,529 | Opening inventory snapshot (Jan 2024) |
| `end_inventory.csv` | 224,489 | Closing inventory snapshot (Dec 2024) |
| `purchase_prices.csv` | 12,261 | Product catalog with vendor purchase prices |
| `purchases.csv` | 2,372,474 | Line-level purchase transactions from vendors |
| `sales.csv` | 12,825,363 | Line-level retail sales transactions |
| `vendor_invoice.csv` | 5,543 | Summary-level vendor invoices with freight and approval data |
| `vendor_sales_summary.csv` | 10,692 | Aggregated vendor-level sales and profitability metrics |

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

---

## ⚙️ Setup

### 1. Clone the repository
```bash
git clone <repo-url>
cd Vendor-Invoice-Intelligence-System
```

### 2. Create and activate virtual environment
```bash
python -m venv venv

# Windows
venv\Scripts\activate

# macOS/Linux
source venv/bin/activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Run notebooks in order
Open Jupyter and run the notebooks sequentially:
```bash
jupyter notebook
```
1. `notebooks/01_data_understanding.ipynb`
2. `notebooks/02_data_cleaning.ipynb`

---

## 🔑 Key Findings (Data Understanding)

- **`vendor_invoice`**: 5,169 out of 5,543 invoices (93.3%) have no approval — `Approval` column repurposed as a risk flag
- **`end_inventory`**: 1,284 rows missing `City` — all belong to Store 46 (TYWARDREATH), filled from `begin_inventory`
- **`purchase_prices`**: 4 rows with `Volume = "Unknown"` — converted to NaN and dropped
- **`purchases`**: Max single purchase quantity is 3,816 units — verified as legitimate bulk orders
- **`sales`**: Largest dataset at 12.8M rows; no missing values

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Python 3.13 | Core language |
| pandas | Data loading, cleaning, transformation |
| sqlite3 | Lightweight relational database |
| Jupyter Notebook | Interactive EDA and pipeline |

---

## 🚧 Roadmap

- [x] Data understanding & profiling
- [x] Data cleaning & SQLite loading
- [ ] Feature engineering (payment delays, freight ratios, vendor scores)
- [ ] Vendor performance analysis & SQL queries
- [ ] Anomaly detection model
- [ ] Interactive dashboard

---

## 👤 Author

**Aditi Barmaiya**
