# Data Engineer Internship — Week 1, Day 2

## Tasks Completed

- Configured Python `logging` to track daily work in `day2.log`
- Read tabular data into pandas from `data/Test.csv`
- Ran an initial data quality check (missing values, duplicate rows, data types)
- Removed exact duplicate rows
- Cleaned messy categorical columns (`City`, `Product`, `Gender`) — inconsistent casing, stray whitespace, and aliases (e.g. `"ktm"` vs `"Kathmandu"`, `"M"`/`"F"` vs `"Male"`/`"Female"`)
- Handled missing values — numeric columns (`Unit_Price`, `Age`) filled with the median, categorical columns (`City`, `Email`) filled with an explicit placeholder
- Converted `Order_Date` to proper datetime type, flagging unparseable dates instead of crashing
- Detected and capped outliers in `Age` and `Unit_Price` using the IQR method
- Engineered a `Total_Amount` feature (`Quantity * Unit_Price`)
- Saved the cleaned dataset to `data/Test_cleaned.csv`

## How to Run

1. Activate the virtual environment:
   ```bash
   source venv/bin/activate
   ```
2. Install dependencies:
   ```bash
   pip install pandas
   ```
3. Open `data_preprocessing_task.ipynb` in VS Code.
4. Select the `venv` interpreter as the notebook kernel.
5. Run the cells in order:
   - Logging setup
   - Load data (`data/Test.csv`)
   - Data quality check
   - Remove duplicates
   - Standardize categorical columns
   - Handle missing values
   - Convert `Order_Date` to datetime
   - Handle outliers
   - Feature engineering (`Total_Amount`)
   - Save cleaned data (`data/Test_cleaned.csv`)
6. Check `day2.log` for a timestamped record of the work done.

## What I Learned

Learned that preprocessing is more than cleaning text — it also means validating types, deciding an explicit policy for missing values instead of guessing, and treating outliers as flagged/capped rather than silently dropped. Practiced the IQR method for outlier detection and `pd.to_datetime(errors="coerce")` for safely parsing inconsistent date formats without crashing the pipeline. Understood why each cleaning step should be logged, so the transformation from raw to clean data stays traceable.

## Challenges

The raw data had several overlapping issues at once — a duplicate row, mixed date formats (`5-Jan-26`, `2026-01-09`, `01/12/2026`, and one invalid date), inconsistent Gender codes (`M`/`F` vs full words), and extreme outliers (an age of 150, a unit price near a million). Deciding the right fix for each column (median fill vs placeholder, capping vs dropping) took more judgment than the mechanical cleaning from Day 1.
