# Content Opportunity & Refresh Scoring Engine: Predicting and Mitigating Content Decay with Machine Learning

**Author:** Netra Mani Pokhrel  
**Track:** Refresh / Content Opportunity Scoring (FlyRank ML Internship Capstone)  
**Repository & Codebase:** [Netrahoni/FlyRankAi-Intern-work-Files](https://github.com/Netrahoni/FlyRankAi-Intern-work-Files)[cite: 1]  
**Data Foundation:** FlyRank Search Warehouse Release (`FlyRank/internship-warehouse`)[cite: 1]

---

## 1. Title + Abstract (5-Sentence Summary)
1. **Research Question:** Can historical search visibility and engagement performance metrics accurately predict which web pages will suffer from content decay? 
2. **Context:** Content portfolios scale rapidly, leaving digital marketing teams unable to manually audit every URL to see where traffic is silently declining. 
3. **Methodology:** Using DuckDB to execute high-performance queries over the FlyRank parquet warehouse, we engineered time-aware features and trained a Random Forest classification model on 79,223 page records. 
4. **Results:** The machine learning model substantially outperformed a heuristic click-through rate (CTR) baseline, raising prediction precision for decaying pages from 47% to 64% and overall accuracy from 51% to 62%. 
5. **Impact:** These probabilities are translated into an automated, impression-weighted opportunity score that ranks content to deliver a prioritized editorial refresh playbook.

---

## 2. Introduction & Problem Statement
Content decay is a primary driver of organic traffic degradation, triggered by evolving user search intent, competitive pressure, or outdated information. 

* **The Operational Bottleneck:** Editorial and SEO teams often rely on reactive reviews, anecdotal complaints, or manual checks to decide when to update a page. 
* **The Decision Supported:** This project supports **proactive content triage** by answering a vital operational question: *Which specific web pages require a human editorial review cycle right now, and on what quantitative basis?*
* **Value Proposition:** By identifying pages showing early signs of traffic loss *before* they bottom out, teams can deploy targeted content refreshes efficiently, rescuing lost visibility and optimizing staff hours.

---

## 3. Data Architecture & Scope
The data for this study was securely accessed through public-safe releases within the FlyRank ML Internship warehouse (`FlyRank/internship-warehouse`)[cite: 1].

* **Source Table:** `fact_content_daily_performance`
* **Historical Feature Window:** November 1, 2025 – April 30, 2026 (Aggregated to form a 6-month historical baseline profile).
* **Future Label Window:** May 1, 2026 – May 31, 2026 (A 1-month forward window used strictly for target derivation).
* **Data Exclusions:** Content rows with fewer than 1,000 historical impressions were excluded from the primary cohort to filter out low-volume statistical noise and prevent model overfitting.
* **Final Dataset Size:** Exactly 79,223 qualified content records.

---

## 4. Methodology & Pipeline Design
To ensure an honest machine learning evaluation without violating time constraints, the modeling framework was carefully structured:

* **Core Features Engineered:**
  * Total Clicks (`gsc_clicks` summed over history)
  * Total Impressions (`gsc_impressions` summed over history)
  * Average Position (`gsc_avg_position` averaged over history)
  * Click-Through Rate (`ctr` calculated as total clicks divided by total impressions)
* **Label Definition (`needs_refresh`):** Formulated as a binary classification target. A page is marked as `1` (Decaying) if its future clicks in May 2026 drop below **85%** of its historical monthly average. Stable or growing content is marked as `0`.
* **Validation & Leakage Control:** A strict chronological train/test split (80% training, 20% testing) was enforced. Because all input features strictly precede the label window in time, future data leakage was completely eliminated.
* **Baseline Comparison:** Evaluated against a heuristic rule-based baseline assuming any page with a CTR falling below the training cohort mean will undergo traffic decay.

---

## 5. Results & Model Performance
When tested on the held-out validation split, the Random Forest classifier achieved a clear performance advantage over the heuristic baseline:

| Evaluation Metric | Heuristic CTR Baseline | Random Forest Classifier |
| :--- | :---: | :---: |
| **Overall Accuracy** | 51% | **62%** |
| **Precision (Decay Class `1`)** | 47% | **64%** |
| **Recall (Decay Class `1`)** | 71% | 36% |
| **Macro Average F1-Score** | 0.51 | **0.59** |

**Key Takeaway:** While the simple rule-based baseline flags many pages, it results in a high false-positive rate. The Random Forest model provides higher **precision (64%)**, giving editorial teams greater confidence that a flagged page genuinely requires remediation.

---

## 6. Limitations & Honest Framing
To maintain scientific integrity, all findings are framed under strict parameters:
* **Nature of Findings:** Observed, measured, and **directional decision-support** rather than absolute causal certainty. 
* **Blind Spots:** Predictions rely strictly on historical Google Search Console visibility signals. The model cannot automatically evaluate external variables such as sudden market disruptions, off-page link campaigns, or unannounced Google core algorithm updates.
* **Operational Rule:** This engine serves as a prioritization mechanism to optimize human workflow, not an automated publishing switch.

---

## 7. Ranked Recommendations: The Action Playbook
To convert model probabilities into tangible business outcomes, an **Opportunity Score** was computed for every test record:
$$\text{Opportunity Score} = \text{Refresh Probability} \times \text{Total Impressions}$$

This formula guarantees that high-traffic pages carrying a high statistical risk of traffic decay bubble directly to the top of the queue:

| Rank | Content Hash ID | Total Impressions | Refresh Probability | Opportunity Score | Recommended Action |
| :---: | :--- | :---: | :---: | :---: | :--- |
| 1 | `content_e241d6415ac9e534...` | 89,858 | 0.4839 | 43,486.15 | Immediate Full Editorial Audit |
| 2 | `content_fd2117c2c6790e4b...` | 56,874 | 0.4468 | 25,412.48 | Immediate Full Editorial Audit |
| 3 | `content_1e921148b5fee86a...` | 43,575 | 0.5060 | 22,031.01 | High-Priority Rewrite & Update |
| 4 | `content_df47d1b976106de4...` | 48,362 | 0.4514 | 21,830.85 | High-Priority Rewrite & Update |
| 5 | `content_c9a0c2fdbdbfb562...` | 56,483 | 0.3786 | 21,386.31 | Metadata & Content Refresh |

---

## 8. Reproducibility & Repository Links
The complete pipeline is modular, open, and reproducible:
* **GitHub Repository:** [Netrahoni/FlyRankAi-Intern-work-Files](https://github.com/Netrahoni/FlyRankAi-Intern-work-Files)[cite: 1]
* **Notebook Architecture:** Executable data prep, feature engineering, modeling, and evaluation notebooks are housed inside the `/work/notebooks/` directory[cite: 1].

---

## 9. Acknowledgments & Data Credit
* **Data Source:** Built on the FlyRank ML Internship dataset (`FlyRank/internship-warehouse`)[cite: 1]. 
* **Platform & Resources:** Learn more about applied search intelligence research tooling at [FlyRank AI](https://flyrank.ai).
