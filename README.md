# Bank Customer Segmentation and Credit Risk

An end-to-end analysis of six years of real, anonymised data from a Czech bank (1993–1998): over 1 million transactions from 4,500 accounts, along with loans, cards and client demographics.

## Questions

1. How does customer activity change over time?
2. What distinct groups of customers does the bank have?
3. Which customers are most likely to default on a loan, and can that be predicted from their behaviour *before* the loan?

## Project structure

| Notebook | Content | Status |
|---|---|---|
| [01 Data preparation](01_data_preparation.ipynb) | SQL loading of 8 related tables, translation from Czech, data-quality checks, first overview | ✅ Done |
| 02 Time series | Monthly activity, balances and seasonality | 🔜 Planned |
| 03 Segmentation | Behavioural features and k-means clustering | 🔜 Planned |
| 04 Credit risk | Default rates by segment and a default-prediction model | 🔜 Planned |

## Key findings so far

- **The data is internally consistent.** Every account has one owner, all records link correctly, and stored balances agree with the transaction amounts to within 3 CZK over six years.
- **Transaction order within a day is unreliable**, so balances are analysed at end of day rather than per transaction.
- **The dataset only contains accounts still open at the end of 1998.** 99.5% of accounts were active in its final three months, so customer churn cannot be studied with this data; the project focuses on credit risk instead.
- **76 of 682 loans (11%) are bad**: unpaid or in debt.

## Tools

Python, SQL (DuckDB), pandas, Matplotlib, scikit-learn

## Data

The data comes from the PKDD'99 Discovery Challenge, a public release of anonymised data from a Czech bank, often called the *Berka dataset*. It is not included in this repository because the transaction file is too large for GitHub.

To run the notebooks:
1. Download the dataset from Kaggle (search for "Berka" or "PKDD 99 financial").
2. Put the 8 CSV files (`account`, `card`, `client`, `disp`, `district`, `loan`, `order`, `trans`) in `data/raw/`.
3. Run `01_data_preparation.ipynb`. It creates the clean tables in `data/clean/` that the other notebooks use.

Requirements: see `requirements.txt`.
