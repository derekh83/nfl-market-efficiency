# NFL Spread & Over/Under Market Efficiency Analysis

Analyzing 6,971 NFL games from 2000–2025 to assess how efficiently the betting market prices point spreads and totals. Built using Python with a focus on quantitative reasoning, feature engineering, and predictive modeling.

---

## Key Findings

- **Favorites covered the spread only 46.9% of the time** — a statistically significant underdog bias (p < 0.0001), though insufficient to exploit profitably after standard vig (-110)
- **The over/under market appears more efficient than the spread market** - with actual totals more symmetrically distributed around the line
- **Wind speed is an underappreciated factor in totals** — games above 15mph show meaningfully lower scoring, suggesting a weather-based signal worth further research
- **Logistic regression and random forest models achieved 52.9% and 52.8% cross-validated accuracy** respectively — consistent with a largely efficient but imperfectly priced market

---

## Visualizations

### Spread Distribution & Spread vs Actual Margin
![Spread Distribution](figures/fig1_spread_distribution.png)

### Cover Rate by Spread Size
![Cover by Spread](figures/fig2_cover_by_spread.png)

### Cover Rate by Season
![Cover by Season](figures/fig3_cover_by_season.png)

### Over/Under vs Actual Total
![Over Under](figures/fig4_over_under.png)

### Feature Importance
![Feature Importance](figures/fig5_feature_importance.png)

### Over/Under Analysis
![Over Under](figures/fig_over_under_analysis.png)

---

## Why the Edge Isn't Exploitable

Even with a statistically significant underdog bias, three factors prevent profitable exploitation:

1. **The vig** — standard -110 odds require 52.4% accuracy to break even; the observed underdog cover rate of 53.1% leaves essentially no margin
2. **Market self-correction** — as sharp money fades favorites, books adjust lines, getting rid of the edge in real time
3. **No opening/closing line data** — true edge is measured by closing line value (CLV); this dataset contains only one spread per game

---

## Methodology

### Data Preparation
- Filtered to 2000–2025 (modern betting era), removing pre-2000 games where market structure differed significantly
- Built a time-aware team name mapping dictionary to handle franchise relocations and rebrands (Washington Redskins → Football Team → Commanders; San Diego → Los Angeles Chargers; Oakland → Las Vegas Raiders)
- Dropped 46 pick'em games (<1% of data) where no spread existed

### Feature Engineering

| Feature | Description |
|---|---|
| `spread_magnitude` | Absolute value of the spread — captures how uneven the matchup is |
| `home_favorite` | Binary flag — favorite has home field advantage |
| `is_playoff` | Binary flag — regular season vs. playoff game |
| `is_division_game` | Binary flag — divisional matchups tend to be more competitive |
| `weather_wind_mph` | Wind speed at game time |
| `over_under_line` | Market-set total — expected game pace and style |

> `total_score` and `over_line` were explicitly excluded from modeling due to **target leakage** — both are derived from the final score, information unavailable at bet placement time.

### Models
- Logistic Regression (interpretable baseline, coefficient analysis)
- Random Forest (captures non-linearities, feature importance)
- 5-fold cross-validation on all models

---

## Repo Structure

```
nfl-market-efficiency/
│
├── nfl_market_efficiency.ipynb   # Full analysis notebook
├── spreadspoke_scores.csv        # Raw dataset (via Kaggle)
│
└── figures/
    ├── fig1_spread_distribution.png
    ├── fig2_cover_by_spread.png
    ├── fig3_cover_by_season.png
    ├── fig4_over_under.png
    ├── fig_over_under_analysis
    └── fig5_feature_importance.png
```

---

## Requirements

```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy
```

---

## Data Source

[NFL Scores and Betting Data — Kaggle](https://www.kaggle.com/datasets/tobycrabtree/nfl-scores-and-betting-data)

---

## Limitations & Future Work

- **No opening/closing line** — adding this would enable true CLV analysis
- **No injury data** — key injuries are among the most impactful variables for line movement
- **No rest/travel data** — short-week games (Thursday Night Football) and cross-country travel affect performance
- **Static historical analysis** — a live implementation would require real-time odds to act on mispriced lines as they move
