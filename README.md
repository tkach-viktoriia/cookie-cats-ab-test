# Cookie Cats — A/B Test Analysis (Progression Gates)

This repository contains an analysis of an A/B test for the mobile game Cookie Cats. The experiment evaluates the impact of shifting the first progression gate (forced delay) from Level 30 (`gate_30`) to Level 40 (`gate_40`). The primary goal is to determine how this structural change affects player engagement and long-term retention.

The full analysis is available in the [Jupyter notebook](https://github.com/tkach-viktoriia/cookie-cats-ab-test/blob/main/cookie_cats_ab_test.ipynb).

## Main Summary & Recommendation

- **Recommendation:** Keep the first gate at Level 30. Do not roll out the Level 40 update to production.
- **Key Finding:** Shifting the gate to Level 40 degrades Day 7 retention from 19.84% to 19.03% — a statistically significant drop that occurs despite no measurable difference in raw engagement volume between the groups.
- **Statistical Certainty:** A bootstrap analysis with 10,000 iterations confirms a 99.87% probability that the Level 30 gate yields higher Day 7 retention.

## Data Audit & Cleaning

The dataset was checked for anomalies before any statistical tests were run. Two categories of records were removed:

1. **Bot/tracking error:** `userid: 6390605` completed 49,854 game rounds within 14 days — physically impossible for a human player. The row was removed to protect baseline metrics from severe distortion.
2. **Zero-round players:** 3,994 users (4.43% of the dataset) installed the game but never completed a single round. Since these players never encountered any progression gate, including them would dilute retention metrics without adding analytical signal. Only engaged users are relevant to evaluating the effect of gate placement.

## Exploratory Data Analysis

| Check | Result |
|---|---|
| Players in gate_30 | 42,762 |
| Players in gate_40 | 43,432 |
| Group balance ratio | 98.45% |
| Zero-round players removed | 3,994 (4.43%) |
| Total players analyzed | 86,194 |
| Day 1 / Day 7 retention correlation | 0.327 |

The near-perfect group balance (98.45%) confirms that randomization was performed correctly, making the groups directly comparable. The moderate correlation (0.327) between Day 1 and Day 7 retention indicates that early engagement is a positive but non-deterministic signal for long-term retention.

The distribution of `sum_gamerounds` is heavily right-skewed, which is why the median is reported alongside the mean throughout this analysis.

## Empirical Metrics & Analysis

### 1. Retention Analysis (Day 1 vs Day 7)

| Metric | gate_30 (Control) | gate_40 (Test) | Delta |
|---|:---:|:---:|:---:|
| Day 1 Retention | 46.75% | 46.22% | −0.53% |
| Day 7 Retention | 19.84% | 19.03% | −0.81% |

The 0.53% drop on Day 1 is minor. The 0.81% decrease on Day 7 is statistically significant and confirmed by bootstrap analysis to be caused by the product change rather than random variance.

### 2. Bootstrap Analysis

To validate the retention difference statistically, a bootstrap simulation was run with 10,000 iterations using resampling with replacement.

| Metric | Result |
|---|:---:|
| Iterations | 10,000 |
| Probability gate_30 > gate_40 (Day 7) | 99.87% |

At 99.87% confidence, the Day 7 retention advantage of gate_30 is not a result of random variance. In practice, a threshold of 95% is considered statistically significant — this result exceeds it by a wide margin.

### 3. User Engagement (Mean vs Median)

| Metric | gate_30 (Control) | gate_40 (Test) | Delta |
|---|:---:|:---:|:---:|
| Mean daily rounds | 3.83 | 3.84 | +0.01 |
| Median daily rounds | 1.29 | 1.29 | 0.00 |

Both mean and median daily rounds are virtually identical across groups. Players in the test group played just as many rounds as the control group. This makes the Day 7 retention drop more meaningful — it shows that the gate position affects long-term motivation rather than short-term playtime.

### 4. Gate Timing (Proxy Estimate)

| Metric | gate_30 (Control) | gate_40 (Test) |
|---|:---:|:---:|
| Mean day of reaching the gate | Day 6.4 | Day 6.6 |
| Median day of reaching the gate | Day 5.8 | Day 6.1 |

A typical gate_30 player hits the forced break around Day 5.8 — just before the Day 7 retention window — giving the game a chance to feel fresh at the moment of measurement. Gate_40 players reach their break on Day 6.1, with less recovery time before the Day 7 check.

*Note: these estimates assume a uniform daily play rate. In reality, players may complete multiple rounds in one session or skip days entirely. Exact gate timing would require raw event-level data with server timestamps.*

### 5. Sleeping Users

3,574 players (4.15%) skipped Day 1 but returned on Day 7. The split is proportional across groups (gate_30: 1,810 / gate_40: 1,764), confirming this reflects organic player behaviour rather than a group-specific effect.

## Contextual Hypotheses & Dataset Limitations

1. **Equal Engagement, Lower Retention:** Both groups played the same number of rounds, yet gate_40 players retained at a lower rate. This suggests the issue is qualitative rather than quantitative — it is not about how much players play, but how they feel about returning.
2. **Timing and Burnout (hypothesis):** Players in the control group encounter a forced break earlier in their progression, which may keep the game feeling fresh closer to the Day 7 retention window. Players in the test group can progress further without interruption, which may lead to faster content saturation. This hypothesis cannot be confirmed without raw event-level data with server timestamps.
3. **Data Aggregation Limitation:** This dataset provides only a 14-day summary and lacks event-driven timestamps for individual level completions. Proving the exact psychological mechanism behind the retention drop would require raw clickstream logs with server timestamps.

## Repository Structure

- `README.md`
- `cookie_cats_ab_test.ipynb` — Main analysis notebook
- `cookie_cats.csv` — Raw dataset
- `cookie_cats_dashboard.png` — Tableau dashboard screenshot

## Dashboard

[View Interactive Dashboard on Tableau Public](https://public.tableau.com/views/cata-btest/CookieCatsABTestAnalysis?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

## Data Source

Dataset sourced from [Kaggle — Cookie Cats A/B Test](https://www.kaggle.com/datasets/mursideyarkin/mobile-games-ab-testing-cookie-cats).
