# Data Engineer Internship — Week 1, Day 3

## Tasks Completed

- Practiced missing value handling on a real-world dataset (`data/tiktok.csv`, TikTok comments)
- Ran an initial missing value audit (`isna().sum()`, percentage breakdown, and correlation between missingness across columns)
- Diagnosed the *mechanism* behind missingness per column rather than applying one blanket method
- Converted `Reply to Which Comment` into a boolean `is_reply` flag instead of imputing a fake ID
- Filled `Reply Count` with `0` and `Pinned to Top` with an explicit `"Not Applicable"` label (not `"No"`) after verifying no `"Yes"` values existed in the data
- Dropped the small number of rows (1.5%) with missing `Comment` text
- Engineered an `is_emoji` column classifying each comment as `"emoji"`, `"text"`, or missing, using the `emoji` library
- Saved the cleaned dataset to `data/tiktok_clean.csv`
- Practiced static-page web scraping with `requests` + `BeautifulSoup` (`quotes.toscrape.com`), including multi-page pagination
- Practiced dynamic-page scraping with Selenium (`quotes.toscrape.com/js`), comparing it against BeautifulSoup's failure on JS-rendered content
- Debugged and resolved a Selenium/Firefox environment issue caused by Snap-packaged Firefox blocking profile access

## How to Run

1. Activate the virtual environment:
   ```bash
   source venv/bin/activate
   ```
2. Install dependencies:
   ```bash
   pip install pandas requests beautifulsoup4 emoji selenium webdriver-manager
   ```
3. Open `data_preprocessing_task.ipynb` in VS Code.
4. Select the `venv` interpreter as the notebook kernel.
5. Run the cells in order:
   - Load data (`data/tiktok.csv`)
   - Missing value audit
   - Handle `Reply to Which Comment`, `Reply Count`, `Pinned to Top`
   - Drop missing `Comment` rows
   - Emoji classification (`is_emoji`)
   - Save cleaned data (`data/tiktok_clean.csv`)
   - BeautifulSoup static scraping demo
   - Selenium dynamic scraping demo

## What I Learned

Learned that missing values shouldn't be handled with a single default method — the first step is always asking *why* a value is missing, since structural missingness (like a reply having no reply count) means something different from a genuine data gap. Also learned to verify assumptions against the actual data rather than guessing a fill value (checking that `"Yes"` never appears before defaulting to `"No"`). On the scraping side, learned the practical difference between BeautifulSoup (static HTML parsing) and Selenium (JS-rendered pages), and that environment setup — browser install, driver versions, Snap vs. native packages — is often the real source of friction, not the code itself.

## Challenges

The TikTok dataset's missingness wasn't random — three columns were missing in exact, correlated patterns tied to whether a comment was a reply, which took extra verification (correlation checks, value-count checks) before deciding on a fix. Comments also mixed emoji-only, text-only, and combined content, requiring a custom classification rule rather than a simple `isna()` check. Selenium setup was the biggest challenge: a Snap-installed Firefox caused a "Profile Missing" crash that wasn't obvious from the error message alone and needed step-by-step isolation (checking `which firefox`, `file`, `dpkg`) to trace back to the Snap sandboxing conflict.
