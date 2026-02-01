# Solution Summary & Answers: MCM 2026 Problem C - Data With The Stars

## 1. Abstract

This study presents a comprehensive mathematical framework to deconstruct the voting mechanism in **Dancing with the Stars**. By employing a **Bayesian Markov Chain Monte Carlo (MCMC) Inverse Optimization** method, we successfully reconstructed the latent fan vote distributions for 34 seasons, generating over 50,000 simulations per elimination event to ensure statistical robustness. This approach allowed us to standardize the comparison between **Rank-based** and **Percentage-based** eras by mapping both to a unified continuous latent popularity scale. 

Our results demonstrate that fan support significantly outweighs technical merit in survival probability, with "Fan Rescue" phenomena becoming mathematically observable when relative popularity exceeds $+1.0\sigma$ above the weekly mean. Furthermore, our **Counterfactual Policy Simulation** reveals that the historical shift to the "Percentage" system inherently amplified the impact of polarized fan bases. We also critically evaluated the **"Judges' Save"** mechanism (introduced in Season 28), finding it effective only for "marginal" controversies but powerless against "super-majority" fan favorites.

---

## 2. Methodology and Implementation

The solution is implemented in a single unified Jupyter Notebook (`Analysis_MCM_Problem_C.ipynb`) utilizing a sequential analytical pipeline.

### 2.1 Fan Vote Reconstruction (Stochastic Inverse Optimization)
*   **Module**: `Analysis_MCM_Problem_C.ipynb` (Sections 1-5)
*   **Method**: We model the unobserved fan votes as a **Latent Variable** ($\mathbf{L}$) that drives the observed elimination results.
    *   **Unified Latent Model**: Fan support is modeled as a continuous composition/probability vector sampled from a **Dirichlet Distribution** ($\mathbf{L} \sim \text{Dir}(\alpha)$).
    *   **Dual-Engine Hybrid Architecture**: To solve the high-dimensional inverse problem, we employ an **Iterative MAP-Guided MCMC** approach. A *Maximum A Posteriori* layout (via temporal inertia) acts as a "Compass" to guide the sampling "Explorer", ensuring convergence to the most sociologically probable solution space.
    *   **Inertia**: To ensure solution stability, we utilize **Informative Priors** updated sequentially: the posterior mean of week $t$ informs the prior for week $t+1$.

### 2.2 Visualization & Statistical Inference
*   **Module**: `Analysis_MCM_Problem_C.ipynb` (Sections 7-8)
*   **Method**: 
    1.  **Hexbin Joint Probability**: Visualizes the density of Judge Score vs. Fan Support to identify the "Safe Zone".
    2.  **Violin Interaction Plots**: Analyzes the distribution of "Relative Popularity Z-Scores" for Safe vs. Eliminated contestants within specific judge score bands.
    3.  **Logistic Regression**: A Generalized Linear Model (GLM) predicts the binary outcome of elimination using standardized predictors.

---

## 3. Results and Response to Problem Requirements

### Requirement 1: "Develop a model to produce estimated fan votes..."
**Solution**:
*   **Model Fidelity**: Our Unified Latent Model achieves **98.1% consistency** (Feasibility Rate) with historical eliminations across 34 seasons, successfully reconstructing 259 out of 264 elimination events.
*   **Robustness Verified**: Sensitivity analysis confirms the model's stability. Varying the regularization parameter ($\lambda$ / Inertia Weight) across a wide range $[10, 500]$ yields convergent estimates for a representative week (Season 19, Week 5). A judge-score perturbation test (up to 20% noise) changed the estimated fan share by **0.5%** for the same week, indicating strong local robustness.
*   **Certainty Dynamic**: The **Posterior Uncertainty Analysis** (Section 6) reveals that the "Rank" system generally exhibits higher stability (lower variance) than the "Percentage" system. In the Percentage era, vague fan preferences result in high volatility distribution tails, which the model correctly captures as increased uncertainty ($\sigma$).

### Requirement 2: "Compare and contrast the two approaches (Rank vs Percentage)..."
**Visual Evidence (from Notebook Section 7)**:
*   **The "Fan Rescue" Threshold**: Our **Violin Interaction Plot** shows a clear bifurcation in the 20-30 Judge Score range. Contestants who survive despite low scores ("Safe" group, Green) consistently exhibit a **Relative Popularity Index > 1.0** (Z-score). Conversely, eliminated contestants (Red) in this range typically have Z-scores below 0. This confirms that fan support acts as a predictable "safety net."
*   **Systemic Bias**:
    *   **Percentage Method**: Structurally favors polarization. A clear "Super-Voter" effect is observed where a single niche demographic can command ~25-30% of the vote share (e.g., Bobby Bones), rendering judge scores mathematically irrelevant.
    *   **Rank Method**: Acts as a dampener. Even if a contestant receives 99% of the fan vote, they are capped at "Rank 1," limiting their ability to offset a last-place judge ranking. However, this dampening is insufficient against extreme outliers.

### Requirement 3 & 4: "Examine specific celebrities" & "Judge Save Impact"
We performed a **Counterfactual Policy Simulation** on the four required anomaly cases, applying three rule sets to each: (1) Rank Sum, (2) Percentage Sum, and (3) Rank Sum + Judges' Save (Season 28+ rule).

#### Case 1: Jerry Rice (Season 2, Week 5)
*   **Context**: Lowest judge score, but survived.
*   **Estimated Fan Support**: **Avg Rank 2.7** (Top Tier).
*   **Simulation Result**: Survived under **ALL** systems, with different predicted eliminations:
    * **Rank**: George Hamilton
    * **Percent**: Drew Lachey
    * **Judge Save**: Tia Carrere
*   **Conclusion**: Jerry Rice represented a "Super-Majority" case. His fan support was so overwhelming (consistently ranking ~2nd-3rd among all contestants) that he never fell into the Bottom 2. The **Judge Save** mechanism is irrelevant here because his high fan rank keeps him out of the danger zone entirely.

#### Case 2: Billy Ray Cyrus (Season 4, Week 6)
*   **Context**: consistently low scores, stayed till 5th place.
*   **Estimated Fan Share**: **~15.9%** (Strong, but not majority).
*   **Simulation Result**:
    *   **Rank/Percent**: Survived (Safe).
    *   **Judge Save**: **ELIMINATED**.
*   **Conclusion**: This is the "textbook" case for the Judge Save. Billy Ray helped displace a stronger dancer (Heather Mills) into the bottom. Under the Judge Save rule, he would have arguably fallen into the Bottom 2 (or low enough) such that judges could intervene. Our simulation shows the Judge Save changes the outcome here, restoring meritocracy.

#### Case 3: Bristol Palin (Season 11, Week 6)
*   **Context**: Survived despite low scores; controversial run.
*   **Estimated Fan Share**: **~16.0%** (Moderate-High).
*   **Simulation Result**:
    *   **Rank Rule**: **Different Outcome** (Jennifer Grey eliminated).
    *   **Percent Rule**: **Different Outcome** (Audrina Patridge eliminated).
    *   **Judge Save**: Jennifer Grey eliminated.
*   **Conclusion**: A chaotic case demonstrating system sensitivity. The Rank method actually *penalized* the high-scoring Jennifer Grey more harshly (due to rank compression) than the Percentage method. Bristol Palin's survival was robust across methods, suggesting the "Rank" fix is not a panacea for mid-tier anomalies. The controversy here is structural: neither system effectively balanced her specific vote distribution.

#### Case 4: Bobby Bones (Season 27, Week 9)
*   **Context**: Won the mirrorball despite consistently bottom-tier scores.
*   **Estimated Fan Share**: **~26.7%** (Massive).
*   **Simulation Result**: Survived under **ALL** systems (Rank, Percent, Judge Save).
*   **Conclusion**: Contrary to the belief that the "Rank" system fixes the Bobby Bones problem, our simulation shows he survives even under Rank rules. His fan engagement was so disproportionate that he secured Rank #1 in votes easily, neutralizing his Rank #Last in scores (1+Last is often safe).
*   **Critical Finding**: The **Judge Save** likely would **FAIL** here too, because Bobby's massive vote count would keep him out of the Bottom 2 entirely, denying judges the opportunity to save anyone against him.

**Summary of Comparisons**:
| Mechanism | Favors | Vulnerability | Effectiveness on Anomalies |
| :--- | :--- | :--- | :--- |
| **Percentage** | Intense Fan Bases | "Super-Voters" (Bobby/Jerry) | Low (Allows extremes to coast) |
| **Rank** | Consistency | Rank Compression (High scores devalued) | Medium (Catches mild outliers, misses huge ones) |
| **Judge Save** | Technical Merit | Requires "Bottom 2" Trigger | High for Billy Ray; **Ineffective for Bobby Bones** |

### Requirement 5: "Analyze the impact of various pro dancers/characteristics..."

To isolate the specific impact of static characteristics (Celebrity Background, Partner Assignment, Demographics) on survival and scoring, we utilized a dual-modeling approach:
1.  **Multivariate Logistic Regression Model** ($\text{logit}(P(Elim))$): Quantifies the "latent advantage" or "penalty" inherent to specific groups for overall survival.
2.  **Comparative OLS Analysis**: Separately models **Judge Scores** and **Latent Fan Support** to identify where expert and audience preferences diverge.

#### 1. Expert vs. Audience Driver Comparison
Our analysis reveals distinct "blind spots" and "preferences" for each voting block:

| Factor | Judge Preference (Technical) | Fan Preference (Popularity) | Comparison Conclusion |
| :--- | :--- | :--- | :--- |
| **Age** | **Linear Decline**: Judges penalize age consistently ($\beta \approx -0.37, p < .001$). Higher age correlates strongly with lower technical scores. | **U-Shaped Paradox**: Fans favor youth, but show a significant "Legacy Rebound" for **Legends (60+)**. | Fans are more forgiving of age than judges, valuing "nostalgia" which experts ignore. |
| **Industry** | **Social Media Advantage**: High technical scores for Digital Stars (+0.49). | **Social Media Dominance**: Overwhelming fan support coefficients (+3.05). | Digital stars benefit from both, but their fan-base advantage is **6x** their technical advantage. |
| **Top Partner** | Favor technical specialists like **Witney Carson** and **Val Chmerkovskiy**. | Favor "personality-driven" pairs like **Jenna Johnson** and **Gleb Savchenko**. | A technical partner boosts the score but doesn't guarantee the "Fan Rescue" needed for survival. |
| **"Model" Background**| Neutral/Moderate scores. | **Structural Deficit**: Highest elimination risk coefficient. | Models struggle to convert physical grace into a "relatable" fan brand. |

#### 2. Highly Significant Demographic Drivers (Detailed)
*   **The Age Paradox**: The **Cubic Polynomial Analysis** on fan support reveals that while physical capabilities decline, the "story arc" of older contestants (e.g., Tommy Chong) creates a survival cushion that is purely popularity-driven.
*   **The Industry Bias**: Athletes and Social Media personalities are the most robust technical performers, while Entrepreneurs and Models face a "popularity ceiling" regardless of their dance quality.

#### 3. The "Pro-Partner" Ecosystem (Trends)
While partner effects showed higher p-values (indicating higher variance), observable trends in coefficients suggest a hierarchy of efficacy:
*   **Protective Trends**: A partnership with **Derek Hough** yields a favorable coefficient ($\beta \approx -0.45$), suggesting a protective buffer.
*   **High-Risk Assignments**: Conversely, patterns suggests higher elimination risks for partners like **Tony Dovolani** or **Edyta Sliwinska**, who historically were assigned lower-potential celebrities.

#### 4. Geographic & Cultural Loyalty
In the multivariate regression, **geographic indicators (state/region)** did **not** show statistically significant effects after controlling for other drivers. The "hometown vote" is statistically noisy compared to industry-based fan bases (e.g., the "Social Media" or "Country Music" blocks).

### Requirement 6: "Propose another system..."
**The "Fan-First" Dynamic Strategy**

Our **Multi-Objective Grid Search** (Notebook Section 9) identified that no single static weight optimizes both "Technical Fairness" and "Retention." We derived our proposal through an exhaustive search of the weight space $w \in [0, 1]$.

1.  **Optimization Logic**: We defined a utility function balancing the **False Elimination Rate** (Technical Outrages) and **Retention Rate** (Commercial Value).
2.  **Finding**: A static weight of 50/50 is sub-optimal because the "needs" of the show change as the season progresses.
3.  **The Proposal: Dynamic Phase-Shift Strategy**:
    *   **Phase 1 (Weeks 1-8)**: **10% Judge / 90% Fan**. Maximizes audience retention and character development.
    *   **Phase 2 (Weeks 9-Finals)**: **45% Judge / 55% Fan**. Shifts toward meritocracy to ensure a "High Quality" winner.
    *   **The "Safety Net" (Bottom 3 Judge Save)**: If a Top-3 technical performer falls into the Bottom 3 of the composite score, judges exercise a save.
4.  **Performance Verification**:
    *   **Eliminates Technical Outrages**: Reduces FER from 5.2% to **0.0%**.
    *   **Maximizes Viewership**: Increases Retention Rate of fan-favorites to **91.2%**.

    ---

    ## 4. Task Coverage from README

    The following sections map directly to the tasks described in [README.md](README.md):

    1. **Estimated fan votes and consistency**
        * **Where**: Notebook Sections 3–6 (Bayesian reconstruction, diagnostics, feasibility rate).
        * **Evidence**: 98.1% feasibility across 264 elimination events; uncertainty analysis by voting era.

    2. **Rank vs Percentage comparison + controversial cases**
        * **Where**: Notebook Sections 6.3 and 8.
        * **Evidence**: Cross-rule match rates, disagreement rates, and four controversy case simulations.

    3. **Impact of partners and characteristics**
        * **Where**: Notebook Section 7 (logistic regression + polynomial popularity analysis).
        * **Evidence**: Partner/industry/age coefficients and non-linear age response curve.

    4. **Proposed improved system**
        * **Where**: Notebook Section 9 (grid search + dynamic strategy).
        * **Evidence**: Optimal static weight line and dynamic strategy performance metrics.

---

## 5. Execution Guide

To reproduce these results, execute `Analysis_MCM_Problem_C.ipynb`.
1.  **Data Ingestion**: Loads `2026_MCM_Problem_C_Data.csv`.
2.  **Simulation**: Runs 50,000 MCMC samples per week to reconstruct latent votes.
3.  **Visualization**: Generates the Hexbin JointPlots and Split-Violin Plots used in this report.
4.  **Policy Simulation**: Executes the counterfactual logic for the 4 specific case studies (Section 8 of the notebook).
