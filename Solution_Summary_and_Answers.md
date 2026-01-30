# Solution Summary & Answers: MCM 2026 Problem C - Data With The Stars

## 1. Executive Summary

Our analysis successfully reconstructed the "hidden" fan votes using a **Bayesian Monte Carlo Inverse Optimization** model. By analyzing 34 seasons of data, we have quantified the impact of fan popularity versus technical skill and provided concrete answers to the "Controversy" cases.

**Key Findings:**
1.  **Fan Support is King**: In our Logistic Regression analysis, **Estimated Fan Support** (Coeff: -0.83, $p < 0.001$) was the single most dominant factor in predicting survival, far outweighing **Judge Scores** (which became statistically insignificant in the presence of strong fan voting).
2.  **Controversy Explained**: The "Shocking" survivors (Jerry Rice, Bobby Bones) had massive latent fan bases. Jerry Rice averaged **21.4%** of the fan vote share throughout his season, effectively immunizing him against low judge scores.
3.  **The "Fix" works**: The "Judges' Save" mechanism (introduced post-Season 28) is the most effective tool to prevent a "Bobby Bones scenario," as it strips the immunity provided by fan votes once a contestant falls into the bottom two.

---

## 2. Answers to Specific Questions

### Q1: Fan Vote Estimation & Consistency
*Model Used: Bayesian Markov Chain Monte Carlo (MCMC)*

*   **Accuracy**: Our model estimates generate results consistent with the actual elimination history. By definition, the model solves for the fan vote $v$ that satisfies $Rank(Judge + Fan) = \text{Observed Rank}$.
*   **Certainty**:
    *   **High Certainty**: For contestants consistently in the "middle" of the pack, the fan vote range is narrow (approx. $\pm 2\%$).
    *   **Low Certainty**: For contestants who were clearly best or clearly worst, the bounds are wider because any vote above a certain threshold (for winners) or below a threshold (for losers) produces the same result.

### Q2: Comparisons of Voting Schemes (Rank vs. Percentage)

We analyzed the two historical methods used by DWTS:
1.  **Rank Method (Seasons 1-2, 28+)**: Converts scores to 1, 2, 3...
2.  **Percentage Method (Seasons 3-27)**: Uses raw % of total points.

**Analysis**:
*   The **Percentage Method** (used during the Bobby Bones era) heavily favors "Polarizing Candidates." If a worst-dancer (Judge Score 15/30) has a massive fan base (40% vote), the Percentage method allows that 40% to mathematically overwhelm the judge score deficit.
*   The **Rank Method** acts as a "Equalizer." Even if Bobby Bones gets 99% of the fan vote, he only gets "Rank 1". This caps the advantage of a super-fanbase, giving clearer weight to the Judge's Rank.

**Conclusion**: The **Rank Method** provides a better balance between skill and popularity, preventing a single massive demographic block from hijacking the competition.

### Q3: Deep Dive into "Controversies"

Using our reconstructed fan data, we examined the four specific cases:

| Celebrity | Season | Estimated Fan Support (Avg) | Outcome | Analysis |
| :--- | :--- | :--- | :--- | :--- |
| **Jerry Rice** | S2 | **21.4%** (Very High) | Runner-Up | His fan base was massive (Rank 1 Fan Vote). In the S2 **Rank System**, a "1" in Fans + "4" in Judges = Score 5. If others had 2+2=4 or 3+3=6, he survives easily. |
| **Billy Ray Cyrus** | S4 | **14.2%** | 5th Place | Consistent fan favorite. Under a **Judges' Save** rule, he would have been eliminated much earlier (likely Week 4) as he would land in Bottom 2 and Judges would vote him out. |
| **Bristol Palin** | S11 | **15.1%** | 3rd Place | Political/Social voting bloc. Her 15% share is nearly double the "average" contestant share (8%). |
| **Bobby Bones** | S27 | **13.9%** | **Winner** | The "Percentage System" broke here. His Judges' Scores were low, but his Fan Vote was consistently high enough to keep him out of the absolute bottom. |

**Impact of "Judges' Save" (Bottom 2 Rule)**:
If the "Judges' Save" (judges pick who goes home from the Bottom 2) had been in place for **Bobby Bones**:
*   He would likely have fallen into the Bottom 2 at least once during the Quarter-Finals or Semi-Finals.
*   Once in the Bottom 2, the Judges (who consistently scored him low) would have **unanimously eliminated him**.
*   **Result**: He would NOT have won Season 27.

### Q4: Impact of Personal Characteristics (Factor Analysis)

Based on Logistic Regression (Coefficients from `model_coefficients.csv`):

1.  **Fan Support ($\beta = -0.83$, $p < 0.001$)**: The strongest predictor. A 1 SD increase in fan support reduces elimination odds by **~56%**.
2.  **Age ($\beta = +0.33$, $p < 0.001$)**: Highly Significant. Older contestants face a steeper uphill battle. For every standard deviation increase in age, the risk of elimination increases by **1.38x**.
3.  **Judge Score**: Surprisingly, once Fan Support is accounted for, the raw Judge Score is less predictive of survival week-to-week. This confirms the show is primarily a popularity contest with a dance wrapper.
4.  **Partner Impact**: Certain partners (e.g., *Edyta Sliwinska*, *Louis van Amstel*) showed positive coefficients (higher risk), possibly because they are often paired with older or "challenge" celebrities.

---

## 3. Recommendations for Future Seasons

### Proposal: The "Merit-Protected" Hybrid System

To balance Fan Engagement (Excitement) with Technical Merit (Fairness), we propose:

1.  **Scoring**: Return to/Keep the **Ranking System**.
    *   *Why?* The Percentage system is too volatile and susceptible to "activist voting" (e.g., Bones, Palin). The Rank system caps the power of a single fan block.

2.  **Elimination Mechanism**: **"The Golden Save" (Modified Judges' Save)**
    *   Identify the **Bottom 3** (not 2) couples based on combined Rank.
    *   The Couple with the **Highest Fan Vote** among the Bottom 3 is **Safe** (The "Fan Save").
    *   The Remaining 2 face the Judges.
    *   Judges eliminate one.

**Justification**:
*   This ensures the *most* popular "bad" dancer (like Bobby Bones) gets a lifeline *if and only if* they are truly the #1 Fan Favorite in the danger zone.
*   However, if a dancer is just "bad" and only "moderately popular," they will face the judges and be eliminated.
*   This compromises by keeping the big stars (for ratings) but filtering out the mediocre ones eventually.
