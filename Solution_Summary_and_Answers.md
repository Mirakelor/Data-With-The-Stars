# Solution Summary & Answers: MCM 2026 Problem C - Data With The Stars

## 1. Abstract

This study presents a comprehensive mathematical framework to deconstruct voting mechanism in **Dancing with the Stars**. By employing a **Bayesian Markov Chain Monte Carlo (MCMC) Inverse Optimization** method, we successfully reconstructed the latent fan vote distributions for 34 seasons, generating over 50,000 simulations per elimination event to ensure statistical robustness. This approach allowed us to standardize the comparison between **Rank-based** and **Percentage-based** eras by mapping both to a unified continuous latent popularity scale. 

Our results demonstrate that fan support significantly outweighs technical merit in survival probability, with "Fan Rescue" phenomena becoming mathematically observable when relative popularity exceeds $+1.0\sigma$ above the weekly mean. Furthermore, our **Counterfactual Policy Simulation** reveals that the historical shift to the "Percentage" system inherently amplified the impact of polarized fan bases, directly enabling controversial outcomes (e.g., the Season 27 Bobby Bones victory). We propose a **"Merit-Protected Hybrid System"** incorporating a **"Golden Save"** mechanism to balance audience engagement with competitive fairness.

---

## 2. Methodology and Implementation

The solution is implemented in a single unified Jupyter Notebook (`Analysis_MCM_Problem_C.ipynb`) utilizing a sequential analytical pipeline.

### 2.1 Fan Vote Reconstruction (Stochastic Inverse Optimization)
*   **Module**: `Analysis_MCM_Problem_C.ipynb` (Sections 1-5)
*   **Method**: We model the unobserved fan votes as a **Latent Variable** ($\mathbf{L}$) that drives the observed elimination results.
    *   **Unified Latent Model**: Fan support is modeled as a continuous composition/probability vector sampled from a **Dirichlet Distribution** ($\mathbf{L} \sim \text{Dir}(\alpha)$).
    *   **Bayesian Inference**: We employ **MCMC Sampling** ($N=50{,}000$) to identify the "Feasible Region" of fan votes that satisfy the mathematical elimination constraints observed historical reality.
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
*   **Model Fidelity**: Our Unified Latent Model achieves >93% consistency with historical eliminations across 34 seasons.
*   **Certainty Dynamic**: The **Posterior Uncertainty Analysis** (Section 6) reveals that the "Rank" system generally exhibits higher stability (lower variance) than the "Percentage" system. In the Percentage era, vague fan preferences result in high volatility distribution tails, which the model correctly captures as increased uncertainty ($\sigma$).

### Requirement 2: "Compare and contrast the two approaches (Rank vs Percentage)..."
**Visual Evidence (from Notebook Section 7)**:
*   **The "Fan Rescue" Threshold**: Our **Violin Interaction Plot** shows a clear bifurcation in the 20-30 Judge Score range. Contestants who survive despite low scores ("Safe" group, Green) consistently exhibit a **Relative Popularity Index > 1.0** (Z-score). Conversely, eliminated contestants (Red) in this range typically have Z-scores below 0. This confirms that fan support acts as a predictable "safety net."
*   **Systemic Bias**:
    *   **Percentage Method**: Structurally favors polarization. A clear "Super-Voter" effect is observed where a single niche demographic can command ~25-30% of the vote share, rendering judge scores mathematically irrelevant (e.g., Bobby Bones, Season 27).
    *   **Rank Method**: Acts as a dampener. Even if a contestant receives 99% of the fan vote, they are capped at "Rank 1," limiting their ability to offset a last-place judge ranking.

### Requirement 3: "Examine... specific celebrities where there was controversy..."
**Policy Simulation Results (Notebook Section 8)**:
*   **Bobby Bones (Season 27)**: The primary case study for system failure. 
    *   *Simulation*: Under the **Percentage System**, his ~25% estimated fan share made him mathematically invincible despite judge scores ~20% lower than competitors.
    *   *Counterfactual*: Our simulation confirms that if the **Rank System** had been used, he would have likely been eliminated in the Semi-Finals due to the inability of "Rank 1" to overcome persistent "Last Place" judge rankings.
*   **Billy Ray Cyrus (Season 4)**: A beneficiary of the Percentage System. His reconstructed fan support shows he commanded a massive share of the audience, negating his last-place judge scores.

### Requirement 4: "Analyze the impact of various pro dancers as well as characteristics..."
**Regression Analysis (Notebook Section 7)**:
*   **Survival Drivers**: **Logistic Regression** results indicate that **Fan Support** ($\beta \approx -0.83$) is a far stronger predictor of survival than **Judge Scores** ($\beta \approx -0.12$).
*   **Demographics**:
    *   **Age**: Statistically significant risk factor. Older celebrities require higher fan support to survive compared to younger ones.
    *   **Industry**: *Country Music* and *Sports* stars show higher baseline popularity intercepts compared to *TV Personalities*.

### Requirement 5: "Propose another system..."
**Solution**: The **"Merit-Protected Hybrid System"** containing a **"Golden Save"**.

---

## 4. Policy Recommendations

To mitigate the risk of technical mediocrity winning due to popularity while maintaining viewer engagement, we recommend:

### Proposal: The "Merit-Protected Hybrid System"
1.  **Core Mechanism: Ranking System**:
    *   Revert to the ordinal ranking system (Used in S1-2, S28+).
    *   *Justification*: As shown in the Bobby Bones counterfactual, the Rank system creates a localized bound on fan power, preventing "runaway" popularity from completely invalidating judge critiques.
2.  **Safety Valve: "The Golden Save"**:
    *   **Rule**: The Bottom 2 couples (based on 50/50 Rank Sum) face a distinct choice.
    *   **Mechanism**: The couple with the **Higher Fan Vote** is identified. If their Fan Rank is *significantly better* (e.g., Top 3 in the night), they are automatically saved ("The People's Choice"). If not, the Judges vote to eliminate one.
    *   *Impact*: This respects the "Voice of the People" for truly beloved stars but prevents consistent weak dancers from coasting to the finals using only a moderate margin of fan support.

---

## 5. Execution Guide

To reproduce these results, execute `Analysis_MCM_Problem_C.ipynb`.
1.  **Data Ingestion**: Loads `2026_MCM_Problem_C_Data.csv`.
2.  **Simulation**: Runs 50,000 MCMC samples per week to reconstruct latent votes.
3.  **Visualization**: Generates the Hexbin JointPlots and Split-Violin Plots used in this report.
4.  **Analysis**: Outputs `estimated_fan_votes.csv` containing the full posterior distributions.
