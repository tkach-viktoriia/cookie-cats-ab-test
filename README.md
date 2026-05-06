# Cookie Cats — A/B Test Analysis (Progression Gates)

This repository contains an analysis of an A/B test for the mobile game Cookie Cats. The experiment evaluates the impact of shifting the first progression gate (forced delay) from Level 30 (`gate_30`) to Level 40 (`gate_40`). The primary goal is to determine how this structural change affects player engagement and long-term retention.

The full analysis is available in the [Jupyter notebook](https://github.com/tkach-viktoriia/cookie-cats-ab-test/blob/main/cookie_cats_ab_test.ipynb).

## Main Summary & Recommendation

- **Recommendation:** Keep the first gate at Level 30. Do not roll out the Level 40 update to production.
- **Key Finding:** Shifting the gate to Level 40 degrades Day 7 retention from 19.84% to 19.03% — a statistically significant drop that is not explained by any difference in raw engagement volume.
- **Statistical Certainty:** A bootstrap analysis with 10,000 iterations confirms a 99.87% probability that the Level 30 gate yields higher Day 7 retention.

## Data Audit & Cleaning

The dataset was checked for anomalies before any statistical tests were run. Two categories of records were removed:

1. **Bot/tracking error:** `userid: 6390605` completed 49,854 game rounds within 14 days — physically impossible for a human player. The row was removed to protect baseline metrics from severe distortion.
2. **Zero-round players:** 3,994 users (4.43% of the dataset) installed the game but never completed a single round. Since these players never encountered any progression gate, including them would dilute retention metrics without adding analytical signal.

## Exploratory Data Analysis

| Check | Result |
|---|---|
| Players in gate_30 | 44,700 |
| Players in gate_40 | 45,489 |
| Group balance ratio | 98.27% |
| Zero-round players removed | 3,994 (4.43%) |
| Day 1 / Day 7 retention correlation | 0.327 |

The near-perfect group balance (98.27%) confirms that randomization was performed correctly, making the groups directly comparable. The moderate correlation (0.327) between Day 1 and Day 7 retention indicates that early engagement is a positive but non-deterministic signal for long-term retention.

The distribution of `sum_gamerounds` is heavily right-skewed, which is why the median is used alongside the mean as the primary engagement metric throughout this analysis.

## Empirical Metrics & Analysis

### 1. Retention Analysis (Day 1 vs Day 7)

| Metric | gate_30 (Control) | gate_40 (Test) | Delta |
|---|:---:|:---:|:---:|
| Day 1 Retention | 46.75% | 46.22% | −0.53% |
| Day 7 Retention | 19.84% | 19.03% | −0.81% |

The 0.53% drop on Day 1 is small. The 0.81% decrease on Day 7 is more substantial and confirmed as statistically significant by the bootstrap analysis — it is caused by the product change, not random variance.

### 2. User Engagement (Mean vs Median)

| Metric | gate_30 (Control) | gate_40 (Test) | Delta |
|---|:---:|:---:|:---:|
| Mean daily rounds | 3.83 | 3.84 | +0.01 |
| Median daily rounds | 1.29 | 1.29 | 0.00 |

Both the mean and median daily rounds are virtually identical across groups. This means the gate shift did not reduce the volume of gameplay — players in the test group played just as many rounds. The retention drop observed on Day 7 is therefore not explained by lower engagement volume, which makes it a more meaningful signal: players were active but still less likely to return, suggesting the gate position affects motivation rather than playtime.

### 3. Gate Timing

| Metric | gate_30 (Control) | gate_40 (Test) |
|---|:---:|:---:|
| Mean day of reaching the gate | Day 6.4 | Day 6.6 |
| Median day of reaching the gate | Day 5.8 | Day 6.1 |

A typical gate_30 player hits the forced break around Day 5.8 — just before the Day 7 retention check — giving the game a chance to feel fresh when the check occurs. A typical gate_40 player reaches their gate on Day 6.1, with less buffer before the Day 7 measurement window closes.

### 4. Sleeping Users

3,574 players (4.15%) skipped Day 1 but returned on Day 7. The split is proportional across groups (gate_30: 1,810 / gate_40: 1,764), confirming this reflects organic player behaviour rather than a group-specific effect.

## Contextual Hypotheses & Dataset Limitations

1. **The Funnel Constraint:** Only ~30-38% of players ever reach Level 30 or 40. The remaining 60-70% left the game before encountering the test variable, meaning the actual negative impact on the active cohort is more severe than the aggregated 0.81% retention drop suggests.
2. **Timing and Burnout:** The gate timing analysis shows that control group players hit their forced break slightly earlier (Day 5.8 vs Day 6.1). This positions the rest mechanic closer to the Day 7 retention window, potentially keeping the game feeling fresh at the moment of measurement.
3. **Data Aggregation Limitation:** This dataset provides only a 14-day summary and lacks event-driven timestamps for individual level completions. Proving the exact psychological mechanism behind the retention drop would require raw clickstream logs with server timestamps.

## Repository Structure

- `cookie_cats_ab_test.ipynb` — Main analysis notebook
- `cookie_cats.csv` — Raw dataset
- `README.md`

## Data Source

Dataset sourced from [Kaggle — Cookie Cats A/B Test](https://www.kaggle.com/datasets/mursideyarkin/mobile-games-ab-testing-cookie-cats).
