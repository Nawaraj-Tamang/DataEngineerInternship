# Week 2 Day 4: Similarity Calculation in Text and Distance Metrics

## Tasks completed
- Generated `sample_comments.csv` (500 rows, 11 columns) with `make_dataset.py`
- Cleaned and tokenized text (lowercase, punctuation removal, stop words)
- Text similarity from scratch: Jaccard, Dice, Overlap, Levenshtein, Cosine (Bag of Words and TF-IDF)
- Built a 500 x 500 TF-IDF similarity matrix, top similar pairs, category level similarity and a query search function
- Distance metrics on scaled numeric features: Euclidean, Manhattan, Chebyshev, Minkowski, Cosine, Mahalanobis (each checked against SciPy)
- Binary distances: Hamming and Jaccard
- Nearest neighbour comparison across metrics and near-duplicate removal with a similarity threshold

## How to run
```bash
pip install pandas numpy scikit-learn scipy matplotlib jupyter
jupyter notebook w2day4_similarity_distance_metrics.ipynb
```
Keep `sample_comments.csv` in the same folder as the notebook. To regenerate the data: `python make_dataset.py`.

## Dataset columns
`comment_id`, `user_id`, `category`, `text`, `likes`, `replies`, `word_count`, `char_count`, `sentiment_score`, `is_pinned`, `posted_at`

## What I learned
- Similarity and distance are two views of the same idea (cosine distance = 1 - cosine similarity)
- Numeric features must be scaled before Euclidean, Manhattan or Chebyshev distance
- TF-IDF cosine similarity recovers topic structure without using the category label
- Levenshtein measures spelling closeness, not meaning

## Challenges
- Different metrics return different nearest neighbours, so the metric has to match the problem
- A full similarity matrix grows with n squared, which does not scale to very large datasets
