# Data Engineer Internship — Week 2, Day 2

## Tasks Completed

- Studied social media analytics metrics across four layers (exposure, engagement, conversion, audience): impressions, reach, views, frequency, engagement rate (by reach, impressions, followers), amplification rate, view rate, completion rate, CTR, conversion rate, and follower growth rate
- Built a dataset generator notebook (`social_media_dataset_generation.ipynb`) that creates a raw, synthetic social media dataset (`social_media_raw.csv`, 1000 rows, 20 columns) with a fixed random seed so the file is identical on every run
- Deliberately planted nine types of raw-data defects for later cleaning practice: missing values, 15 duplicate rows, inconsistent `platform` and `content_format` labels, three mixed datetime formats, numbers stored as text (`"1.2K"`, `"N/A"`), reach greater than impressions, negative shares, zero followers, and inflated likes (outliers)
- Built `tiktok_social_media_analysis.ipynb`, which filters the raw file to TikTok (234 posts after removing duplicates) and applies only the minimum cleaning needed for valid metrics, logging every fix
- Calculated all 11 metrics per post and at aggregate level, using total numerator / total denominator over valid rows instead of averaging per-post rates
- Visualized the results: exposure-to-conversion funnel, engagement rate by content format, account comparison, time-of-day and weekday patterns, monthly trend, video completion by length, frequency vs. engagement, engagement mix, and distribution of per-post engagement rate
- Interpreted the results with findings generated from the numbers (for example, video earned 9.08% engagement rate by reach vs. 3.59% for text, while posting time and video length had little effect)
- Built a single-post scorecard comparing the best-performing TikTok post against the TikTok average using an index (100 = average)
- Scope: only the TikTok analysis is done; Instagram, YouTube, and Facebook have not been analyzed yet, and the full step-by-step cleaning notebook is still to come

## How to Run

1. Activate the virtual environment:
   ```bash
   source venv/bin/activate
   ```
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
   (`requirements.txt` contains `pandas`, `numpy`, `matplotlib`, `ipykernel`)
3. Open `social_media_dataset_generation.ipynb` in VS Code, select the `venv` interpreter as the kernel, and run all cells. This creates `social_media_raw.csv` in the same folder.
4. Open `tiktok_social_media_analysis.ipynb` (keep it in the same folder as the CSV) and select the same kernel.
5. Use **Restart** and then **Run All** so the cells execute top to bottom in order:
   - Setup and load
   - Minimum cleaning
   - Per-post metrics
   - Overall scorecard
   - Charts and findings (funnel, format, account, timing, trend, video, frequency and mix)
   - Best and worst posts, single-post scorecard, summary

## What I Learned

Learned that impressions and reach measure different things, and that frequency (impressions ÷ reach) links them, so the same engagement count gives a different rate depending on the denominator: reach for content quality, followers for comparing accounts. Group-level rates must be calculated as total numerator ÷ total denominator, because averaging per-post rates lets small posts distort the result. Invalid values (such as reach greater than impressions) are better set to missing and excluded only from the metrics they affect than deleting whole rows. Also learned to check sample size before trusting a comparison, since small groups like text posts give weak conclusions, and that content format mattered far more than posting time in this data.

## Challenges

The main error was `KeyError: 'engagements' not in index` when running the scorecard cell. The cause was not the scorecard itself: the cleaning cell begins with `df = raw.copy()`, so running cells out of order or re-running it after the metrics cell wipes the columns created later, and the scorecard then fails. Restarting the kernel and running all cells in order fixed it. The other difficulty was deciding how to treat invalid and missing values without silently changing the results, which meant logging each fix and reporting how many posts each metric was based on.
