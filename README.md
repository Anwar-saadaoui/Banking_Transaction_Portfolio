# FinanceCore SA — Data Cleaning Pipeline

End-to-end data cleaning and feature engineering pipeline for FinanceCore SA's raw transaction exports.

---

## Dataset

`bank_transactions.csv` — 2060 rows, 16 columns covering customer transactions, currencies, exchange rates, and risk profiles.

| Column | Description |
|---|---|
| `transaction_id` | Unique transaction identifier |
| `client_id` | Customer identifier |
| `date_transaction` | Transaction date (mixed formats) |
| `montant` | Transaction amount |
| `devise` | Currency |
| `taux_change_eur` | Exchange rate to EUR |
| `montant_eur` | Amount in EUR |
| `categorie` | Transaction category |
| `produit` | Product type |
| `agence` | Branch name |
| `type_operation` | Credit or debit |
| `statut` | Transaction status |
| `score_credit_client` | Customer credit score |
| `segment_client` | Customer segment |
| `solde_avant` | Balance before transaction |
| `taux_interet` | Interest rate |

---

## Pipeline Overview

### 1. Exploration
- Shape, dtypes, descriptive stats
- Missing value rate per column (with bar chart)
- Duplicate `transaction_id` detection

### 2. Cleanup
- Removed duplicates — kept first occurrence per `transaction_id`
- Unified date formats (`DD/MM/YYYY` and `YYYY-MM-DD`) → `datetime`
- Fixed comma decimal separator in `montant` → `float`
- Stripped `EUR` text suffix from `solde_avant` → `float`
- Normalized `devise` to uppercase, `segment_client` to Title Case
- Collapsed extra whitespace in `agence`
- Imputed missing values: **median** for numerics, **mode** for categoricals

### 3. Outlier Detection
- IQR (×1.5) on `montant`
- Domain rule `[0, 850]` on `score_credit_client`
- Outliers flagged in `is_anomaly` column — **retained, not deleted**

### 4. Feature Engineering

| New Column | Description |
|---|---|
| `year`, `month`, `quarter`, `day_of_week` | Extracted from `date_transaction` |
| `montant_eur_verified` | `montant / taux_change_eur` (recalculated) |
| `montant_eur_diff` | Difference vs existing `montant_eur` |
| `risk_category` | `Low` ≥700 / `Medium` 580–699 / `High` <580 |
| `net_balance_eur` | Total credits − debits per client |
| `nb_transactions` | Transaction count per client |
| `avg_montant_eur` | Average EUR amount per client |
| `nb_produits_distinct` | Distinct products per client |
| `rejection_rate_agence` | Rejection rate per branch |

### 5. Export
- Final dataset → `financecore_clean.csv`

---

## Output

| File | Description |
|---|---|
| `financecore_clean.csv` | Cleaned and enriched dataset |
| `financecore_pipeline.ipynb` | Full pipeline notebook |

---

## Getting Started

```bash
git clone https://github.com/your-username/financecore-pipeline
cd financecore-pipeline
pip install pandas numpy matplotlib seaborn
```

Place `bank_transactions.csv` in the root directory, then run the notebook top to bottom.

---

## Stack

![Python](https://img.shields.io/badge/Python-3.10-blue)
![Pandas](https://img.shields.io/badge/Pandas-2.x-lightblue)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange)
