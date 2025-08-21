# WNBA
Traditional plus-minus is a noisy measure of a player's impact. This project creates a more stable and predictive metric by first calculating a robust, regularized adjusted plus-minus (RAPM) using Bayesian methods. This "gold standard" RAPM is then used to train a model on common box score statistics, player archetypes (derived via clustering), and positional data. The final output is a WNBA Box Plus-Minus (BPM) metric that estimates a player's per-100-possessions contribution to their team's point differential, based on their observable box score contributions.

The model is built in five key stages:

1. Data Collection
A variety of public data was aggregated, including:

Play-by-Play Data: For calculating raw plus-minus and assembling lineup units.

Box Score Statistics: Traditional stats (PTS, REB, AST, STL, BLK, TOV, etc.) for all players.

Player Metadata: Position, height, and team affiliation.

2. Bayesian RAPM Calculation
The foundation of the model is a Regularized Adjusted Plus-Minus (RAPM) calculated using Bayesian hierarchical methods.

Input: Possession-by-possession lineup data and net point differential.

Method: A Bayesian regression model which treats each player's true plus-minus impact as a parameter to be estimated.

Regularization: A weakly informative prior (e.g., Gaussian) is placed on player RAPM coefficients. This shrinks estimates for players with limited minutes toward a mean of zero, significantly reducing noise and providing more reliable estimates for role players.

3. Offensive Archetype Clustering
To capture playstyles beyond the box score, offensive roles were categorized using unsupervised learning.

Method: K-Means clustering was applied to per-possession usage statistics.

Features: Usage rate, assist rate, turnover rate, rebound rate, etc.

Output: Each player is assigned a primary offensive archetype (e.g., "Primary Ball-Handler," "Spot-Up Shooter," "Roller/Cutter") for each season. These archetype labels are used as features in the final regression.

4. Regression onto RAPM
The Bayesian RAPM serves as the target variable for training the descriptive Box Plus-Minus model.

Target Variable: Bayesian RAPM (from Step 2).

Features: Box Score Stats: Per-100-possession counts for all major statistics.

Categorical Features: One-hot encoded player archetypes (from Step 3) and primary position.

Model: Linear regression (with controls, fixed effects for year, and interaction effects between position/archetype and the box score stats) is used to find the coefficients that best map the box score features to the "true" impact measured by RAPM.

5. Adjustments
The raw regression output undergoes two final adjustments to enhance interpretability and fairness:

Positional Adjustment: The league-average BPM for each position (G, SG, F, PF, C) is calculated, and players' scores are adjusted so that the minutes-weighted average for each position is zero. This allows for more intuitive comparisons within a position.

Team Strength Adjustment: A slight adjustment is made to account for overall team strength, ensuring that players on very good or very bad teams are not overly penalized or rewarded for factors outside their individual box score contributions.
