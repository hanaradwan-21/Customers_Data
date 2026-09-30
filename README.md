# Customer Purchases Dashboard

An interactive **Streamlit** dashboard built on a messy customer-purchases dataset. The project covers the full flow: profiling raw data, cleaning it, and presenting the result, including a **before vs. after** view that shows exactly what the cleaning changed.

<!-- Add a screenshot: ![Dashboard](screenshots/dashboard.png) -->

## Project structure

```
customer-dashboard/
├── app.py                          # Streamlit dashboard
├── requirements.txt
└── data/
    ├── Customers_Fakedata.csv      # raw data (2,150 rows, 12 columns)
    └── Cleaned_Customers_Data.xlsx # cleaned data (2,049 rows, 9 columns)
```

## Dashboard features

| Tab | What it shows |
|---|---|
| **Overview** | Monthly revenue trend, revenue by product category, rating distribution |
| **Customers** | Gender split, purchase amount distribution, age distribution, filtered data table with CSV download |
| **Data quality** | Raw vs. cleaned comparison: row counts, problem values per column, and a grouped bar chart |

Sidebar filters (category, gender, purchase date) update every KPI and chart: purchases, total revenue, average purchase, average rating.

## The data

The raw file simulates real-world customer data with these columns: `CustomerID`, `Name`, `Age`, `Gender`, `Email`, `Phone`, `PurchaseAmount`, `PurchaseDate`, `ProductCategory`, `Rating`.

### Problems found in the raw data

- **Duplicates:** 50 fully duplicated rows.
- **Invalid ages:** values of `-1` and `200`, plus missing values (1,643 of 2,150 rows unusable).
- **Inconsistent gender labels:** `M`, `male`, `Male`, `F`, `female`, `Female`, plus 273 missing values.
- **Missing values:** purchase amount (101), product category (577), rating (329), phone (1,078).
- **Out-of-range ratings:** a value of `10` on a 1-5 scale (298 rows).
- **Invalid dates:** impossible dates such as `32/13/2020` (122 rows).
- **Structural noise:** an empty `Unnamed` column and a duplicated `Gender` column with a trailing space in its name.

### Cleaning steps applied

1. Dropped the empty and duplicate columns, and dropped `Phone` (over 50% missing and not needed for analysis).
2. Standardized gender into `Male`, `Female`, and `Not Specified`.
3. Replaced invalid ages (`<= 0` or `> 100`) and missing ages with the median age (55).
4. Removed rows with a missing `PurchaseAmount`, since imputing revenue would distort totals.
5. Filled missing categories with `Unclassified`.
6. Replaced missing and out-of-range ratings with `Not Rated`.
7. Converted `PurchaseDate` to a proper datetime type.

### Before vs. after

| Issue | Raw | Cleaned |
|---|---:|---:|
| Rows | 2,150 | 2,049 |
| Distinct gender labels | 6 | 3 |
| Missing purchase amount | 101 | 0 |
| Missing / invalid age | 1,643 | 0 real gaps (1,569 filled with the median) |
| Missing category | 577 | 0 real gaps (551 marked `Unclassified`) |
| Missing / invalid rating | 627 | 0 real gaps (597 marked `Not Rated`) |

## Known limitations

Being transparent about the cleaning is part of the analysis:

- **Heavy age imputation:** about 77% of ages are the median value (55), so age analysis is unreliable. The dashboard excludes imputed ages from the age histogram and says so on the page.
- **Placeholders are not observations:** `Unclassified`, `Not Rated`, and `Not Specified` keep rows usable but carry no information.
- **Leftover duplicates:** 46 duplicated rows remain in the cleaned file (mostly repeated `CustomerID` values), so some customers may be double counted.
- **Invalid dates mislabeled:** 116 invalid purchase dates were replaced with the text `Not Rated` instead of a null value. The dashboard converts them to missing dates, so they are excluded from the monthly trend only.

Suggested next steps: drop the remaining duplicates, use `NaT` for invalid dates, and consider leaving invalid ages empty instead of imputing them.

## Run it locally

```bash
git clone <your-repo-url>
cd customer-dashboard
pip install -r requirements.txt
streamlit run app.py
```

The app opens at `http://localhost:8501`.

## Tech stack

Python, pandas, Plotly, Streamlit

## Author

Hana, Artificial Intelligence student, Kafr El-Sheikh University.
