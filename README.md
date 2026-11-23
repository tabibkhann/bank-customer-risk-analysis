Bank Customer Segmentation and Credit Risk

An end-to-end analysis of real, anonymised data from a Czech bank covering 1993 to 1998: 4,500 accounts, 1,056,320 transactions, 682 loans, and information about cards and customers.

I wanted to answer three questions:

How did customer behaviour change over the six years?
What different types of customers does the bank have?
Can the bank predict which loans will go bad, using only what it knew when it granted the loan?
Notebooks
Notebook	What it does
01 Data preparation	Loads the 8 related tables, translates the Czech codes, decodes gender and age from the birth number, checks data quality, and builds an account-level table with SQL (DuckDB)
02 Time series	Growth, activity per customer, seasonality, balances over time, negative balances, and the shift from cash to transfers
03 Segmentation	Builds behaviour features per customer, groups customers with k-means, and describes the segments
04 Credit risk	Predicts loan default from pre-loan data only, compares logistic regression and random forest, and chooses a decision threshold
Key findings

The bank grew, but its customers didn't change much. Active accounts grew from 96 to about 4,500, but a typical account made about 3.5–4 transactions a month in every year. The typical end-of-month balance rose slowly, to about 38,500 CZK by the end of 1998.

Four customer segments:

Segment	Share	Description
Established savers	52%	Good income, highest balances, mix of cash and transfers
Cash-only earners	11%	Similar income, but almost only cash and unstable balances
Low-income customers	20%	Low income, low but steady balances
Pensioners	16%	A pension every month, median age 71, no loans

Age was not used for the clustering, yet the pensioners came out with a median age of 71, which is a good sign the segments are meaningful. The Cash-only earners hold 57 of the 76 bad loans.

Bad loans can be predicted from pre-loan behaviour. A random forest using only data from before each loan reached a ROC-AUC of 0.91 in cross-validation, and 0.90 when trained on 1993–1996 loans and tested on 1997 loans. The strongest warning signs were:

the balance dropping low in the 90 days before the loan
a balance that swings a lot
using only cash and not paying bills by transfer
having already gone negative

Income itself made almost no difference.

Choosing a threshold. I assumed a bad loan loses 50% of its amount and a refused good loan loses 10% in profit. With those illustrative costs, the best threshold catches 62 of the 76 bad loans and refuses 94 good ones. That lowers the cost of mistakes from 7.8 million CZK (approving everything) to 2.7 million. A simple rule, "never lend to an account that has been negative", gets to 5.0 million.

Things I found in the data
Transactions within a day are stored in the wrong order. A simple check that each balance equals the previous balance plus the amount failed for half the transactions. Checked per day, the balances match to within 3 CZK over six years, so I only use end-of-day balances.
January looks artificial. Every January, cash and card withdrawals jump to 4–8 times their normal level, with a group of accounts suddenly making 6–8 withdrawals instead of 1–2. I left January out of the customer features.
All transfers happen between the 5th and 14th of the month, and all loan repayments on the 12th.
Only surviving accounts are included. 99.5% of accounts were active in the last three months, so customer churn can't be studied with this data. That's why the project focuses on credit risk instead.
Most of the link between negative balances and bad loans is leakage. Every bad-loan account went negative at some point and no good-loan account did, but for 49 of the 76 bad loans that only happened after the loan was granted. Only 27 were already negative before.
How the model was kept honest
Every feature in the credit risk model uses only data from before the loan date, checked with assert statements.
Models were compared with repeated stratified 5-fold cross-validation (25 folds), plus a time-based test on a later year.
Loans from 1998 had little time to go bad (2.5% bad, against 10–14% earlier). Removing them barely changed the results.
Age, gender and district were left out of the model on purpose.
Limitations
There are only 76 bad loans, so all results are uncertain by a few points.
The model performs better than credit models usually do on real data. Several patterns above suggest the dataset was partly generated, so real-world performance would likely be lower.
The costs used to choose the threshold are assumptions, not the bank's real figures.
How to run
Download the dataset (PKDD'99 Discovery Challenge financial data, also called the Berka dataset) from Kaggle: ADD-KAGGLE-LINK-HERE
Put the 8 CSV files (account, card, client, disp, district, loan, order, trans) in data/raw/. The data isn't included here because trans.csv is too large for GitHub.
Set up the environment (macOS or Linux):
bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
Run the notebooks in order, 01 to 04. Notebook 01 saves clean tables to data/clean/, which the other notebooks use.
Tools

Python, SQL (DuckDB), pandas, Matplotlib, statsmodels, scikit-learn
