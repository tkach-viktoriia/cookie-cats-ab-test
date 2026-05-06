# Cookie Cats — A/B Test Analysis (Progression Gates)

This repository contains an analysis of an A/B test for the mobile game Cookie Cats. The experiment evaluates the impact of shifting the first progression gate (forced delay) from Level 30 (`gate_30`) to Level 40 (`gate_40`). The primary goal is to determine how this structural change affects player engagement and long-term retention.

## Main Summary & Recommendation
* **Recommendation:** Keep the first gate at Level 30. Do not roll out the Level 40 update to production.
* **Key Finding:** Shifting the gate to Level 40 systematically degrades Day 7 retention (dropping from 19.02% to 18.20%) and reduces the typical player's daily activity by 5.8%.
* **Statistical Certainty:** A Bootstrap analysis with 10,000 iterations confirms a 100% probability that the Level 30 gate yields higher long-term retention. 

## Data Audit & Outlier Removal
Before running any statistical tests, the dataset was checked for technical anomalies. One critical outlier was detected: `userid: 6390605`, a user who completed 49,854 game rounds within the 14-day window. Because this is physically impossible for a human player, the row was flagged as a tracking error or bot and removed to protect the baseline metrics and variance from severe distortion.

## Empirical Metrics & Analysis

### 1. Retention Analysis (Day 1 vs Day 7)
* **Day 1 Retention:** Control (`gate_30`) = 44.82% | Test (`gate_40`) = 44.23%
* **Day 7 Retention:** Control (`gate_30`) = 19.02% | Test (`gate_40`) = 18.20%

While the 0.59% drop on Day 1 is minor, the 0.82% decrease on Day 7 is statistically significant. The bootstrap distribution confirms that this reduction is caused by the product change rather than random variance.

### 2. User Engagement (Mean vs Median)
The distribution of total game rounds is heavily right-skewed due to a small cohort of highly active players ("whales"). Comparing averages directly is misleading, making the median a much more reliable metric for a typical user:
* **Mean daily rounds:** 3.67 (Control) vs 3.66 (Test) — shows virtually no difference.
* **Median daily rounds:** 1.21 (Control) vs 1.14 (Test) — reveals a clear 5.8% drop in active engagement.

## Contextual Hypotheses & Dataset Limitations
A deeper look at the data structure highlights a few important constraints that modify how we interpret these results:

1. **The Funnel Constraint:** Only ~30-38% of players in the dataset ever reach the 30th or 40th levels. This means 60-70% of users left the game before even encountering the test variable. Therefore, the gate shift did not affect them, meaning the actual negative impact on the active cohort of players is much more severe than the aggregated 0.82% retention drop shows.
2. **Timing and Burnout:** Proxy calculations of player speed show that a typical user reaches the gate around Day 6. In the Control group, this forced break occurs right before the Day 7 retention check, keeping the game fresh. In the Test group, players can play continuously without a natural break, leading to content saturation and quicker burnout by Day 6.
3. **Data Aggregation Limitation:** This public dataset provides only a 14-day summary and lacks event-driven timestamps for individual level completions. While the correlation between the gate shift and player drop-off is mathematically verified, proving the exact psychological mechanism of burnout would require raw clickstream logs with server timestamps.

---
📊 **Tableau Dashboard:** [Link to Interactive Visualization](YOUR_TABLEAU_LINK_HERE)
