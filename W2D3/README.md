# Data Engineer Internship — Week 2, Day 3

## Tasks Completed

- Built `week2_day3_normalization_postgres.ipynb`, which normalizes the TikTok comments export (800 comments, 9 columns) and loads it into PostgreSQL
- Reused the Week 2 Day 1 connection pattern: `.env` credentials, `DBConnection` class, `with_retry` decorator, logging to `w2day3.log`, and an `ensure_database` step that creates the `bmc` database if it is missing
- Loaded the three ID columns as strings, because 19-digit IDs lose precision as `float64`; with the default read 0 of 369 reply links matched a comment ID, with string read all 369 matched
- Cleaned the data: renamed columns, filled the structural blanks in `reply_count` and `is_pinned` (they are blank on exactly the 369 reply rows), parsed timestamps, converted Yes/No flags to 1/0, and normalized text (NFKC, lowercase, collapsed whitespace) while keeping emoji
- Added derived features: comment length, word count, hour of day, and cyclical `hour_sin` / `hour_cos`
- Applied scaling normalization (Min-Max, Z-score, log1p + Min-Max, Robust) on the skewed count columns and verified every manual result against scikit-learn
- Showed train/test leakage by fitting the scaler on the training split only, then transforming the test split
- Checked the flat CSV against 1NF, 2NF, and 3NF, and found that `reply_count` is derived (stored value matched the real reply count for all 431 top-level comments) and `user_id` repeats across rows
- Designed and created the Postgres schema: `users`, `comments` (PK, FK to users, self-referencing FK, CHECK constraint, indexes), `comment_features`, and a `comment_stats` view that computes `reply_count` on demand
- Loaded 435 users, 800 comments, and 800 feature rows in a single transaction with `TRUNCATE` first, so the notebook can be re-run safely
- Verified in SQL that no information was lost (view equals original `reply_count`, JOIN row count matches, all IDs stored exactly, zero orphan replies) and confirmed that the foreign key, CHECK, and primary key constraints reject bad inserts
- Redid Min-Max and Z-score inside Postgres using window functions (`MIN() OVER ()`, `STDDEV_POP() OVER ()`) and confirmed they match the pandas results
- Saved the final dataset as `tiktok_normalized.csv` (800 rows, 28 columns)

## How to Run

1. Activate the virtual environment:
   ```bash
   source venv/bin/activate
   ```
2. Install dependencies:
   ```bash
   pip install pandas numpy scikit-learn psycopg2-binary python-dotenv
   ```
3. Make sure a PostgreSQL server is running, and create a `.env` file next to the notebook (add `.env` to `.gitignore`):
   ```
   DB_HOST=127.0.0.1
   DB_PORT=5432
   DB_NAME=bmc
   DB_USER=postgres
   DB_PASSWORD=your_password_here
   ```
4. Keep `tiktok.csv` in the same folder as the notebook and open `week2_day3_normalization_postgres.ipynb` in VS Code with the `venv` kernel.
5. Use **Restart** and then **Run All** so the cells execute top to bottom in order:
   - Logging, credentials, and database connection
   - Load and clean the CSV, format normalization
   - Scaling normalization
   - Schema design and creation in Postgres
   - Load the normalized data
   - Verification, constraint tests, and scaling in SQL
   - Save `tiktok_normalized.csv` and close the connection

## What I Learned

Learned that there are two kinds of normalization: scaling (Min-Max, Z-score, log, Robust) changes the range of numbers, while database normalization (1NF to 3NF) removes redundancy and derived data. The scaling method should be chosen from the distribution, for example log1p for skewed counts like `digg_count`, and scalers must be fit on training data only to avoid leakage. In the schema, derived data such as `reply_count` is better computed with a view than stored, and constraints (PK, FK, CHECK) actually protect the data, as the failed test inserts showed. I also learned that 19-digit IDs must be read as strings and stored as `BIGINT`, and that the same scaling can run inside Postgres with window functions and give identical results.

## Challenges

The biggest problem was the 19-digit IDs: pandas read them as floats, which silently broke every parent-reply link until I loaded them as strings. Some columns could not be scaled the normal way: `is_pinned` is constant (all 0), and `digg_count` has an IQR of 0, so Robust scaling does not apply. The self-referencing foreign key forced me to insert top-level comments before replies, and the load had to be one transaction so that a failure leaves the tables untouched and `with_retry` can safely run it again. Also, `users` holds only `user_id` because the CSV has no other user attributes.
