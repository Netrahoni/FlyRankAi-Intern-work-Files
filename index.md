# FlyRank Capstone: Content Opportunity & Refresh Scoring Engine

## Abstract
This research investigates whether historical search visibility and engagement signals can predict future content decay. By engineering time-aware features from the FlyRank search warehouse using DuckDB, we trained a Random Forest classifier to identify pages at risk of a traffic drop. The model outperformed a heuristic CTR-based baseline, achieving an overall accuracy of 62% and a precision of 64% on decay prediction. The resulting output is a ranked action engine that scores and prioritizes decaying pages for editorial refresh based on impression-weighted opportunity scores.

## Introduction / Problem Statement
Content decay is a primary driver of organic traffic loss. Editorial teams often rely on reactive reviews or manual checks to decide when to update a page. This project supports proactive decision-making by replacing guesswork with a machine-learning-driven opportunity score that prioritizes high-traffic pages showing early signs of decline.

## Data
Data was sourced from the FlyRank ML Internship dataset (`FlyRank/internship-warehouse`). We utilized daily performance metrics (`fact_content_daily_performance`).
*   **Time Windows:** Historical features were aggregated over a 6-month window (November 1, 2025 – April 30, 2026). Future performance labels were evaluated in a subsequent 1-month window (May 1, 2026 – May 31, 2026).
*   **Exclusions:** URLs with fewer than 1,000 historical impressions were excluded to filter out low-volume noise and prevent overfitting.
*   **Total Volume Analyzed:** 79,223 qualified content records.

## Methodology
*   **Features:** Total Clicks (`gsc_clicks`), Total Impressions (`gsc_impressions`), Average Position (`gsc_avg_position`), and Calculated Click-Through Rate (`ctr`).
*   **Label Definition:** Binary classification where `needs_refresh` = 1 if future clicks dropped below 85% of the historical monthly average, and 0 otherwise.
*   **Validation Design:** Strict chronological time-aware split to completely eliminate data leakage.
*   **Baseline:** A heuristic baseline assuming pages with a CTR below the training set average will undergo traffic decay.

## Results
The Random Forest classifier significantly outperformed the heuristic baseline, improving precision for identifying decaying pages from 47% up to **64%**.
*   **Baseline Accuracy:** 51%
*   **Random Forest Accuracy:** 62%

## Limitations
This model provides **directional decision-support**, not absolute causal certainty. Predictions are based on historical GSC search behavior and do not automatically account for external market shifts, competitor updates, or Google core algorithm updates.

## Ranked Recommendations: The Action Playbook
The action engine computes an Opportunity Score ($\text{Refresh Probability} \times \text{Total Impressions}$) to prioritize high-risk, high-traffic pages. The top targets for immediate content review are:

1. **`content_e241d6415ac9e534...`** (Opportunity Score: ~486,115)
2. **`content_fd2117c2c6790e4b...`** (Opportunity Score: ~446,874)
3. **`content_1e921148b5fee86a...`** (Opportunity Score: ~506,030)
4. **`content_df47d1b976106de4...`** (Opportunity Score: ~451,405)
5. **`content_c9a0c2fdbdbfb562...`** (Opportunity Score: ~378,631)

## Reproducibility
*   **Repository:** [Netrahoni/FlyRankAi-Intern-work-Files](https://github.com/Netrahoni/FlyRankAi-Intern-work-Files)[cite: 1]
*   **Notebooks:** Pipeline and modeling scripts are stored in the `/work/notebooks/` directory.

## Acknowledgments & Data Credit
Built on the FlyRank ML Internship dataset. Data source and documentation available via [https://flyrank.ai](https://flyrank.ai).
