# Cookie Cats — A/B Test Analysis (Progression Gates)

This repository contains an analysis of an A/B test for the mobile game Cookie Cats. The experiment evaluates the impact of shifting the first progression gate (forced delay) from Level 30 (`gate_30`) to Level 40 (`gate_40`). The primary goal is to determine how this structural change affects player engagement and long-term retention.

The full analysis is available in the [Jupyter notebook](https://github.com/tkach-viktoriia/cookie-cats-ab-test/blob/main/cookie_cats_ab_test.ipynb).

## Main Summary & Recommendation

- **Recommendation:** Keep the first gate at Level 30. Do not roll out the Level 40 update to production.
- **Key Finding:** Shifting the gate to Level 40 systematically degrades Day 7 retention (dropping from 19.02% to 18.20%) and reduces the typical player's daily activity by 5.8%.
- **Statistical Certainty:** A bootstrap analysis with 1,000 iterations confirms a 100% probability that the Level 30 gate yields higher long-term retention.

## Data Audit & Outlier Removal

Before running any statistical tests, the dataset was checked for technical anomalies. One critical outlier was detected: `userid: 6390605`, a user who completed 49,854 game rounds within the 14-day window. Because this is physically impossible for a human player, the row was flagged as a tracking error or bot and removed to protect the baseline metrics from severe distortion.

## Empirical Metrics & Analysis

### 1. Retention Analysis (Day 1 vs Day 7)

| Metric          | gate_30 (Control) | gate_40 (Test) | Delta  |
|-----------------|:-----------------:|:--------------:|:------:|
| Day 1 Retention | 44.82%            | 44.23%         | −0.59% |
| Day 7 Retention | 19.02%            | 18.20%         | −0.82% |

While the 0.59% drop on Day 1 is minor, the 0.82% decrease on Day 7 is statistically significant. The bootstrap distribution confirms that this reduction is caused by the product change rather than random variance.

### 2. User Engagement (Mean vs Median)

The distribution of total game rounds is heavily right-skewed due to a small cohort of highly active players. Comparing averages directly is misleading, making the median a more reliable metric for a typical user:

| Metric              | gate_30 (Control) | gate_40 (Test) | Delta |
|---------------------|:-----------------:|:--------------:|:-----:|
| Mean daily rounds   | 3.67              | 3.66           | −0.3% |
| Median daily rounds | 1.21              | 1.14           | −5.8% |

## Contextual Hypotheses & Dataset Limitations

1. **The Funnel Constraint:** Only ~30-38% of players ever reach Level 30 or 40. This means 60-70% of users left the game before encountering the test variable, so the actual negative impact on the active cohort is more severe than the aggregated 0.82% retention drop suggests.
2. **Timing and Burnout:** Proxy calculations of player speed show that a typical user reaches the gate around Day 6. In the Control group, this forced break occurs right before the Day 7 retention check, keeping the game fresh. In the Test group, players can progress continuously, leading to content saturation and quicker burnout.
3. **Data Aggregation Limitation:** This dataset provides only a 14-day summary and lacks event-driven timestamps for individual level completions. Proving the exact psychological mechanism of burnout would require raw clickstream logs with server timestamps.

## Repository Structure

- `cookie_cats_ab_test.ipynb` — Main analysis notebook
- `cookie_cats.csv` — Raw dataset
- `README.md`

## Data Source

Dataset sourced from [Kaggle — Cookie Cats A/B Test](https://www.kaggle.com/datasets/mursideyarkin/mobile-games-ab-testing-cookie-cats).
