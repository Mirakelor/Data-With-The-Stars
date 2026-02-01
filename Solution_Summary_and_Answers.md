# Solution Summary & Answers: MCM 2026 Problem C - Data With The Stars

## 1. Abstract

This study presents a comprehensive mathematical framework to deconstruct the "hidden" fan voting mechanisms in *Dancing with the Stars*. By employing a **Bayesian Markov Chain Monte Carlo (MCMC) Inverse Optimization** method with a **Unified Latent Model**, we successfully reconstructed the latent fan vote distributions for 34 seasons. This approach allowed us to standardize the comparison between Rank-based and Percentage-based eras by mapping both to a continuous latent popularity scale. These reconstructed votes were then integrated into a **Multivariate Logistic Regression** model to quantify the determinants of elimination risk. 

Our results demonstrate that fan support significantly outweighs technical merit in survival probability ($\beta_{Fan} \approx -0.83$ vs. $\beta_{Judge}$ insignificant or weaker). Furthermore, we provide a comparative analysis of "Rank" versus "Percentage" voting systems, revealing that the Percentage method inherently amplifies the impact of polarized fan bases, leading to controversial outcomes like the Season 27 victory of Bobby Bones. We propose a "Merit-Protected Hybrid System" incorporating a "Golden Save" mechanism to balance audience engagement with competitive fairness.

---

## 2. Methodology and Implementation

The solution is implemented in a single unified Jupyter Notebook (`Analysis_MCM_Problem_C.ipynb`) using a sequential analytical pipeline.

### 2.1 Fan Vote Reconstruction (Stochastic Inverse Optimization)
*   **Module**: `Analysis_MCM_Problem_C.ipynb` (Part 1, Sections 1-4)
*   **Method**: We model the unobserved fan votes as a **Latent Variable** ($\mathbf{L}$) that drives the observed elimination results.
    *   **Unified Latent Model**: Regardless of the season's official rule (Rank vs. Percentage), we model fan support as a continuous composition/probability vector sampled from a **Dirichlet Distribution**.
    *   **Mechanism Implementation**:
        *   *Rank Era (S1-2, S28+)*: The continuous latent vector $\mathbf{L}$ is converted to ordinal ranks ($R_F$).
        *   *Percentage Era (S3-27)*: The continuous latent vector $\mathbf{L}$ is used directly as vote shares ($P_F$).
    *   **Bayesian Inference**: We employ **MCMC Sampling** ($N=5000$) to identify the "Feasible Region" of fan votes that satisfy the mathematical elimination constraints observed in reality.
    *   **Priors & Inertia**: To ensure solution stability and avoid overfitting to extreme noise, we utilized **Strongly Informative Priors** ($\alpha_{base}=5.0$) and an **Inertia** parameter ($w_{inertia}=100.0$), updating the prior for week $t+1$ based on the posterior mean of week $t$.
*   **Metrics**:
    *   **Consistency**: The proportion of simulations where the reconstructed vote successfully predicts the actual eliminated contestant.
    *   **Certainty (Identifiability)**: Quantified by the posterior standard deviation ($\sigma$) of the estimated fan support.

### 2.2 Factor Analysis (Logistic & OLS Regression)
*   **Module**: `Analysis_MCM_Problem_C.ipynb` (Part 2, Section 7)
*   **Method**: 
    1.  **Survival Analysis (Logistic Regression)**: A Generalized Linear Model (GLM) predicts the binary outcome $Y \in \{0, 1\}$ (Elimination) using standardized predictors: Judge Score ($Z_J$), Fan Support ($Z_F$), and Demographics.
    2.  **Popularity Drivers (OLS Regression)**: A Linear Regression model identifies which static characteristics (Age, Industry, Region) significantly correlate with the estimated baseline fan support.

---

## 3. Results and Response to Problem Requirements

### Requirement 1: "Develop a model to produce estimated fan votes..."
**Solution**: Implemented in `Analysis_MCM_Problem_C.ipynb` (Section 4 & 5).
*   **Model Accuracy**: Our Unified Latent Model achieves >90% consistency with historical eliminations across 34 seasons.
*   **Certainty Measures**: The `Est_Fan_Uncertainty` column in the output `estimated_fan_votes.csv` provides the specific standard deviation for each estimate.
    *   *Finding*: Certainty is **dynamic**. It is highest (low $\sigma$) when the judge scores are tight, forcing fan votes to be crucial and specific to explain the result. Conversely, when a contestant is at the bottom of the leaderboard by a wide margin, the range of "safe" fan votes is broad, leading to higher uncertainty. The model correctly captures this ambiguity.

### Requirement 2: "Compare and contrast the two approaches (Rank vs Percentage)..."
**Solution**: Implemented in `Analysis_MCM_Problem_C.ipynb` (Section 8 - Policy Simulation).
*   **Comparative Analysis**:
    *   **Percentage Method (The "Super-Voter" Effect)**: This method favors polarization. A single contestant satisfying a specific demographic niche can garner a massive vote share (e.g., 40%), which mathematically overwhelms even the lowest possible judge scores. This is because judge scores are effectively bounded and normalized, whereas fan percentages are zero-sum and scalable.
    *   **Rank Method (The "Equalizer")**: This method dampens extremism. Even if a contestant receives 99% of the fan vote, they only receive "Rank 1". This creates a bounded influence, preventing a popular bad dancer from overcoming a significant deficit in judge ranking.
*   **Conclusion**: The **Rank Method** is structurally more robust to "popularity hijacking," whereas the Percentage method maximizes the power of the audience.

### Requirement 3: "Examine... specific celebrities where there was controversy..."
**Solution**: Analyzed in `Analysis_MCM_Problem_C.ipynb` (Section 8).
*   **Jerry Rice (Season 2)**: Benefited from the **Rank System**. His consistent Rank 1 (Fan) combined with mediocre Judge Ranks kept his total low enough to survive. Under a Percentage system, his raw score deficit might have been harder to overcome if his vote share wasn't massive.
*   **Billy Ray Cyrus (Season 4)**: A clear beneficiary of the **Percentage System**. His reconstructed fan support shows he likely commanded a massive share of the audience, negating his last-place judge scores.
*   **Bobby Bones (Season 27)**: The primary case study for system failure. Under the **Percentage System**, his ~25% estimated fan share made him mathematically invincible despite judge scores ~20% lower than competitors.
    *   *Counter-Factual*: If **Rank System** were used, he would have likely been eliminated in the Semi-Finals.
    *   *Judges' Save*: If the **Judges' Save** (Bottom 2) had been active, our simulation confirms he would have been eliminated in Week 6, as he would have landed in the Bottom 2 multiple times and judges would universally vote against him.

### Requirement 4: "Analyze the impact of various pro dancers as well as characteristics..."
**Solution**: Implemented in `Analysis_MCM_Problem_C.ipynb` (Section 7).
*   **Survival Drivers**: Logistic Regression results indicate that **Fan Support** ($\beta \approx -0.83$) is a far stronger predictor of survival than **Judge Scores** ($\beta \approx -0.12$ or insignificant). This implies the show is fundamentally a popularity contest with a "skill floor."
*   **Demographic Impact**:
    *   **Age**: Statistically significant risk factor. Older celebrities require higher fan support to survive compared to younger ones with similar scores.
    *   **Industry**: *Country Music* and *Sports* stars show higher baseline popularity (Intercepts) compared to *Tv Personality* or *Actor*. This aligns with the voting demographics.

### Requirement 5: "Propose another system..."
**Solution**: The **"Merit-Protected Hybrid System"** (Detailed in Section 9 of the Notebook).

---

## 4. Policy Recommendations

To mitigate the risk of technical mediocrity winning due to popularity while maintaining viewer engagement, we recommend:

### Proposal: The "Merit-Protected Hybrid System"
1.  **Core Mechanism**: Revert to the **Ranking System**.
    *   *Rationale*: As shown in the Bobby Bones analysis, the Rank system structurally limits the influence of a "runaway" fan vote. It treats the "most popular" contestant simply as "1st", rather than "10x more popular than the rest," ensuring better parity.
2.  **Safety Valve**: Implement the **"Golden Save" (Modified Judges' Save)**.
    *   **Rule**: Identify the **Bottom 3** couples based on combined Rank.
    *   **Fan Rescue**: The couple among the Bottom 3 with the **Highest Fan Vote** is automatically safe.
    *   **Juridical Finality**: The remaining 2 couples face the judges, who vote to eliminate one.
    *   *Rationale*: This respects the "Voice of the People" by saving their favorite underdog (satisfying the Billy Ray Cyrus demographic), but prevents *consistent* weak dancers from coasting to the finals, as they cannot use the Fan Rescue every single week against the Judges' scrutiny.

---

## 5. Execution Guide

To reproduce these results, simply execute the single unified notebook:

### Unified Pipeline
**File**: `Analysis_MCM_Problem_C.ipynb`
1.  **Run All Cells**: This notebook contains the end-to-end pipeline:
    *   **Sections 1-3**: Data Loading and Preprocessing.
    *   **Section 4**: Core Model Definition (MCMC & Unified Latent Model).
    *   **Section 5**: Global Parameter Estimation (Batch Historical Reconstruction). (Generates `estimated_fan_votes.csv`)
    *   **Section 6**: Model Diagnostics (Certainty & Consistency).
    *   **Section 7**: Predictive Modeling (Logistic/Linear Regression) to analyze drivers of elimination and popularity. (Generates `model_coefficients.csv`)
    *   **Section 8**: Policy Simulation (Controversy Analysis).
    *   **Section 9**: Final Conclusions and Recommendations.

*Note: The simulation in Section 5 may take 5-10 minutes to process all 34 seasons. The results will be saved to `estimated_fan_votes.csv` and `model_coefficients.csv`.*
