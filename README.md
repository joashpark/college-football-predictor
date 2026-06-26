# College Football Prediction Model

## Overview
This project predicts NCAA football game outcomes using historical game data and interpretable rating-based modeling.  
The pipeline combines Elo updates, recency-weighted team performance, home-field advantage, and conference-level strength adjustments to produce winner predictions and win probabilities.

## Dataset
- Source file used in notebook: `cfb_box-scores_2002-2025.csv`
- Coverage: seasons from **2002 to 2025**
- Example fields:
  - game metadata: `season`, `week`, `date`, `game_type`
  - teams: `home`, `away`
  - outcomes: `score_home`, `score_away`
  - context: `conf_home`, `conf_away`, rankings, and team stats

## Methodology

### 1) Feature Engineering
- Created binary target: `home_win = 1(score_home > score_away)`
- Estimated historical home advantage from average score margin:
  - `mean(score_home - score_away)`
- Added recency weighting:
  - `recency_weight = season - min(season) + 1`

### 2) Team Strength Modeling
- Initialized Elo ratings for each team at 1500.
- Updated ratings game-by-game using:
  - expected score from Elo difference
  - actual game result (`home_win`)
  - update factor `K = 20`
  - recency-scaled update magnitude
- Computed an additional weighted point-differential rating (`team_ratings`) for probabilistic scoring.

### 3) Probability and Prediction
- Win probability function uses a logistic transform of rating differential plus home advantage.
- Conference strength is estimated from historical conference home win rates and used as an adjustment in final prediction logic.
- Final prediction returns the team with probability > 0.5.

### 4) Evaluation
- Generated per-game predictions across the dataset.
- Compared predicted winner vs actual winner.
- Reported accuracy: **~0.6197**.

### 5) Simulation
- Implemented single-game simulation from Elo probabilities.
- Added season-level win aggregation from simulated games.
- Added a simple 4-team playoff bracket simulation.

## Results
- Model accuracy is approximately **62%** on evaluated games.
- Rating distributions and top-team charts show meaningful separation in team strength.
- Probability outputs enable upset detection and scenario simulation.

## Visualizations Included
- Top teams by model rating
- Distribution of predicted win probabilities
- Distribution of team strength ratings

## Limitations
- Evaluation is primarily in-sample; no strict temporal holdout split is enforced.
- Probability calibration metrics (e.g., Brier score, log loss) are not yet included.
- Some notebook headings are stored as code cells instead of markdown.

## Future Improvements
- Add train/validation split by season (time-aware backtesting).
- Tune Elo hyperparameters (`K`, scaling, home offset).
- Incorporate richer features (injuries, returning production, travel/rest effects).
- Improve probability calibration and add confidence intervals.
- Build automated weekly prediction and playoff simulation pipeline.
