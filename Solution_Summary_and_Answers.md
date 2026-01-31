# Solution Summary & Answers: MCM 2026 Problem C - Data With The Stars

## 1. Abstract

This study presents a comprehensive mathematical framework to deconstruct the "hidden" fan voting mechanisms in *Dancing with the Stars*. By employing a **Bayesian Markov Chain Monte Carlo (MCMC) Inverse Optimization** method, we successfully reconstructed the latent fan vote distributions for 34 seasons. These reconstructed votes were then integrated into a **Multivariate Logistic Regression** model to quantify the determinants of elimination risk. Our results demonstrate that fan support significantly outweighs technical merit in survival probability ($\beta_{Fan} = -0.83$ vs. $\beta_{Judge}$ insignificant). Furthermore, we provide a comparative analysis of "Rank" versus "Percentage" voting systems, revealing that the Percentage method inherently amplifies the impact of polarized fan bases, leading to controversial outcomes like the Season 27 victory of Bobby Bones. We propose a "Merit-Protected Hybrid System" incorporating a "Golden Save" mechanism to balance audience engagement with competitive fairness.

---

## 2. Methodology and Implementation

The solution is implemented in a single unified Jupyter Notebook (`Analysis_MCM_Problem_C.ipynb`) using a sequential analytical pipeline.

### 2.1 Fan Vote Reconstruction (Inverse Optimization)
*   **Module**: `Analysis_MCM_Problem_C.ipynb` (Part 1, Sections 1-4)
*   **Method**: We model the unobserved fan votes as a random variable satisfying the elimination constraints.
    *   **Rank Era (Seasons 1-2, 28+)**: Modeled via uniform permutation sampling compliant with rank-sum constraints.
    *   **Percentage Era (Seasons 3-27)**: Modeled via a Dirichlet distribution where the prior for week $t+1$ is updated using the posterior mean of week $t$ (Bayesian Update).
    *   **Optimization**: To address potential overfitting and extreme outliers, we implemented a **Robust Bayesian Prior** (using Dirichlet $\alpha=2.0$ instead of 1.0) and an **Inertia** parameter. This ensures that our estimates reflect the natural stability of fan bases and penalizes unrealistic 0% or 100% vote shares unless empirically necessary to explain an elimination.
*   **Metrics**:
    *   **Consistency**: Verification that estimated votes reproduce the observed elimination.
    *   **Certainty**: Quantified by the standard deviation ($\sigma$) of the posterior distribution.

### 2.2 Factor Analysis (Logistic Regression)
*   **Module**: `Analysis_MCM_Problem_C.ipynb` (Part 2, Section 7)
*   **Method**: A Generalized Linear Model (GLM) with a Logit link function is trained to predict the binary outcome $Y \in \{0, 1\}$ (Elimination).
    *   **Predictors**: Standardized Judge Scores, Reconstructed Fan Support, Age, Partner, Industry.

---

## 3. Results and Response to Problem Requirements

### Requirement 1: "Develop a model to produce estimated fan votes..."
**Solution**: Implemented in `Analysis_MCM_Problem_C.ipynb` (Section 4).
*   **Method**: Sequential MCMC Simulation.
*   **Consistency Check**: Our model achieves >90% consistency with historical eliminations.
*   **Certainty Measures**: The `Estimated_Fan_Std` column in the output `estimated_fan_votes.csv` provides the specific uncertainty for each estimate. We found that certainty is **not** constant; it is highest (low variance) for mid-tier contestants and lowest (high variance) for clear front-runners or bottom-dwellers, as the mathematical constraints on them are looser.

### Requirement 2: "Compare and contrast the two approaches (Rank vs Percentage)..."
**Solution**: Implemented in `Analysis_MCM_Problem_C.ipynb` (Section 8 - Policy Simulation).
*   **Analysis**:
    *   The **Percentage Method** creates a "Super-Voter Effect." A contestant with a massive fan base (e.g., 30% of total vote) can mathematically absorb almost any deficit in Judges' scores because the judge scores are normalized to a similar 0-1 range but have lower variance.
    *   The **Rank Method** dampens this effect. Even with 99% of the vote, a contestant only receives "Rank 1". This forces a closer correlation between combined rank and judge rank.
*   **Conclusion**: Extensive simulation confirms the **Rank Method** is fairer for balancing skill vs. popularity, while the Percentage method favors popularity.

### Requirement 3: "Examine... specific celebrities where there was controversy..."
**Solution**: Analyzed in `Analysis_MCM_Problem_C.ipynb` (Section 8).
*   **Jerry Rice (S2)**: Saved by the Rank System's integer math. His Rank 1 (Fans) + Rank 4 (Judges) = 5 was sufficient to beat others.
*   **Bobby Bones (S27)**: The primary beneficiary of the **Percentage System**. His reconstructed fan share ($\approx 14\%$) was high enough to overwhelm his poor judge scores (~20% deficit relative to leaders).
    *   **Counter-Factual**: If the **Rank System** had been used in S27, our simulation shows he would have been at much higher risk.
    *   **Judges' Save**: If the **Judges' Save** (Bottom 2) had been active, our simulation confirms he would have been eliminated in the Quarter-Finals, as he would have landed in the Bottom 2 and Judges would universally vote against him.

### Requirement 4: "Analyze the impact of various pro dancers as well as characteristics..."
**Solution**: Implemented in `Analysis_MCM_Problem_C.ipynb` (Section 7).
*   **Fan Support**: The dominant factor. $\beta \approx -0.83$ ($p < 0.001$).
*   **Age**: Significant negative impact. Older celebrities face higher elimination risk ($\beta \approx +0.33$), requiring higher fan support to survive.
*   **Industry**: Country singers and Athletes tend to have higher baseline Fan Support (intercepts in Linear Regression) compared to Models or Actors.

### Requirement 5: "Propose another system..."
**Solution**: The **"Merit-Protected Hybrid System"** (Detailed in Recommendations).

---

## 4. Policy Recommendations

To mitigate the risk of technical mediocrity winning due to popularity while maintaining viewer engagement, we recommend:

### Proposal: The "Merit-Protected Hybrid System"
1.  **Scoring Mechanism**: Revert to the **Ranking System**.
    *   *Rationale*: It structurally limits the influence of a "runaway" fan vote, ensuring that the Contestant with the highest fan votes cannot mathematically negate a last-place finish in judging as easily as in the Percentage system.
2.  **Safety Valve**: Implement the **"Golden Save" (Modified Judges' Save)**.
    *   **Rule**: Identify the **Bottom 3** couples based on combined Rank.
    *   **Fan Rescue**: The couple among the Bottom 3 with the **Highest Fan Vote** is automatically safe.
    *   **Juridical Finality**: The remaining 2 couples face the judges, who vote to eliminate one.
    *   *Rationale*: This respects the "Voice of the People" by saving their favorite underdog, but prevents *multiple* weak dancers from surviving simultaneously, as the second-worst will inevitably face the judges.

---

## 5. Execution Guide

To reproduce these results, simply execute the single unified notebook:

### Unified Pipeline
**File**: `Analysis_MCM_Problem_C.ipynb`
1.  **Run All Cells**: This notebook contains the end-to-end pipeline:
    *   **Sections 1-3**: Data Loading and Preprocessing.
    *   **Section 4-5**: Monte Carlo Simulation (Inverse Optimization) to generate `estimated_fan_votes.csv`.
    *   **Section 6**: Model Evaluation (Certainty & Consistency).
    *   **Section 7**: Predictive Modeling (Logistic/Linear Regression) to analyze drivers of elimination and popularity.
    *   **Section 8**: Policy Simulation (Controversy Analysis).
    *   **Section 9**: Final Conclusions and Recommendations.

*Note: The simulation in Section 5 may take 5-10 minutes to process all 34 seasons. The results will be saved to `estimated_fan_votes.csv` and `model_coefficients.csv`.*
