# TrackScore 

An XGBoost-powered hit prediction tool for Billboard songs, with Spotify API integration and an interactive Streamlit app for musicians to analyse their tracks.

TrackScore predicts the probability that a song will reach the Billboard Hot 100 Top 20 based on its audio characteristics, release era, genre, and artist track record — combining 60+ years of chart history with real-time Spotify data.

## How It Works

1. **Data** — 327,895 weekly Billboard Hot 100 entries (1958–2021) merged with Spotify audio features for 29,503 unique tracks.
2. **Hit Definition** — A song is labelled a "hit" if it reached Peak Position ≤ 20 on the Billboard Hot 100.
3. **Feature Engineering** — Derived features include artist historical hit rate, release decade, and primary genre (extracted from Spotify's genre tags).
4. **Modelling** — Two models were trained and compared: Logistic Regression (baseline) and XGBoost (main model).
5. **Explainability** — Feature importance analysis identifies which signals drive predictions most.
6. **Deployment** — A Streamlit app fetches a song's audio features live from the Spotify API and returns a hit probability with supporting analysis.

7. ## Model Performance

| Model | F1 Score | ROC-AUC | Accuracy |
|---|---|---|---|
| Logistic Regression | 0.637 | 0.845 | 71% |
| XGBoost | 0.760 | 0.926 | 84% |

XGBoost significantly outperforms the linear baseline, confirming that hit prediction is a non-linear problem driven by complex interactions between artist reputation, era, and audio characteristics.



**Top predictive features:** artist hit rate, release decade, genre, and Spotify popularity score, with individual audio features (danceability, energy, valence, etc.) contributing smaller but meaningful signal.

## Limitations & Honest Caveats

This project is built with transparency in mind. A few important caveats:

- **Data leakage avoided, not eliminated entirely.** `Weeks_on_Chart` was deliberately excluded from the feature set as it is only knowable after a song has already charted. `spotify_track_popularity` is retained as it can reflect early streaming momentum for new releases, though in this historical dataset it is a recency-weighted score that decays for older songs regardless of their original chart success.

- **Artist hit rate dominates predictions.** The single strongest predictor is an artist's historical hit rate, computed from their track record in the training data (1958–2021). For artists not present in this dataset (including most contemporary independent musicians), the model defaults to the dataset average (27%), which limits predictive power for genuinely new or unknown artists — arguably the audience this tool is most useful for.

- **Training data ends in 2021.** The model has not seen post-2021 chart trends. Genre, sound, and listening behaviour continue to evolve, and predictions for very recent musical styles should be interpreted cautiously.

- **Audio features alone are weak predictors.** Individual correlation analysis showed that audio characteristics (danceability, energy, valence, etc.) have minimal standalone correlation with hit status. XGBoost captures non-linear combinations of these features, but the model's strongest signals come from contextual features (artist reputation, era, genre) rather than the sound of the song itself.

- **Release Cadence Advisor is rule-based, not model-driven.** The release frequency recommendations shown in the app are general industry guidelines derived from genre conventions, not predictions from the trained model. The dataset does not contain release frequency data needed to make this data-driven.

