# Movie Recommendation System

A movie recommendation system built with Python, pandas, and scikit-learn.
It performs exploratory data analysis (EDA), cleans movie metadata, and
builds both a **content-based recommender** (TF-IDF + cosine similarity) and
an optional **collaborative filtering recommender** (matrix factorization on
user ratings). Recommendations are evaluated qualitatively with sample
queries, and reference code is included for packaging the recommender as a
Flask API.

## Project Structure

```
.
├── Movie_Recommendation_System.ipynb   # Main notebook (all steps below)
├── movies.csv                          # Optional: MovieLens movies file
├── ratings.csv                         # Optional: MovieLens ratings file
└── README.md
```

## Dataset

This project is designed to work with the [MovieLens dataset](https://grouplens.org/datasets/movielens/)
(e.g. the "ml-latest-small" set), which provides `movies.csv` and
`ratings.csv` files.

- If `movies.csv` and `ratings.csv` are present in the same folder as the
  notebook, they are loaded automatically.
- If they are **not** found, the notebook falls back to a small built-in
  sample dataset (25 movies, 30 synthetic users) so every cell still runs
  end to end without any external download.

To use the real dataset:
1. Download `ml-latest-small.zip` from the MovieLens site.
2. Extract `movies.csv` and `ratings.csv` into this project folder.
3. Re-run the notebook.

## What the Notebook Does

1. **Load Dataset** – Loads MovieLens files if available, otherwise
   generates the sample dataset.
2. **Exploratory Data Analysis (EDA)** – Checks dataset shape, missing
   values, rating distribution, and most common genres, with plots.
3. **Metadata Cleaning** – Combines `genres` and `overview` into a single
   `tags` text field used for content-based features.
4. **Content-Based Recommender** – Builds TF-IDF vectors over `tags` and
   computes cosine similarity between all movies. Includes a
   `recommend_content_based(title, top_n)` function.
5. **Qualitative Evaluation** – Runs the recommender on sample movies
   (e.g. *Toy Story*, *The Matrix*, *Titanic*, *The Dark Knight*) and
   displays the top recommendations for manual inspection.
6. **Collaborative Filtering (Optional)** – Builds a user-item ratings
   matrix and applies Truncated SVD to find similar movies based on rating
   patterns. Includes a `recommend_collaborative(title, top_n)` function.
7. **Flask API (Optional)** – Reference code for exposing the content-based
   recommender through a `/recommend` endpoint (not executed in the
   notebook itself).

## Requirements

- Python 3.9+
- pandas
- numpy
- scikit-learn
- matplotlib
- jupyter (to run the notebook)
- flask (only needed if you run the optional API)

Install everything with:

```bash
pip install pandas numpy scikit-learn matplotlib jupyter flask
```

## Running the Notebook

```bash
jupyter notebook Movie_Recommendation_System.ipynb
```

Run all cells from top to bottom (Cell -> Run All). No external services or
API keys are required.

## Running the Optional Flask API

The Flask section of the notebook prints a reference `app.py`. To actually
run it:

1. Copy the printed code into a new file named `app.py` in the project
   folder (next to `movies.csv`).
2. Install Flask: `pip install flask`
3. Run the server:

   ```bash
   python app.py
   ```

4. Query it, for example:

   ```
   GET http://127.0.0.1:5000/recommend?title=Toy Story (1995)&top_n=5
   ```

   Response:

   ```json
   {
     "query": "Toy Story (1995)",
     "recommendations": [
       {"title": "Toy Story 2 (1999)", "genres": "Adventure|Animation|Children|Comedy|Fantasy"},
       {"title": "Monsters, Inc. (2001)", "genres": "Animation|Children|Comedy|Fantasy"}
     ]
   }
   ```

## Example Usage (in the notebook)

```python
recommend_content_based("Toy Story (1995)", top_n=5)
recommend_collaborative("Toy Story (1995)", top_n=5)
```

## Notes

- The content-based recommender relies purely on movie metadata (genres and
  overview text), so it works even for movies with few or no ratings.
- The collaborative filtering recommender relies on user rating patterns,
  so it works best when a movie has a reasonable number of ratings in
  `ratings.csv`.
- Similarity scores in both recommenders range from 0 to 1, where higher
  values indicate greater similarity.

## Possible Extensions

- Combine content-based and collaborative scores into a hybrid recommender.
- Add a simple front-end (HTML form) on top of the Flask API.
- Use richer metadata (cast, director, keywords) if using the full TMDB
  dataset instead of MovieLens.
