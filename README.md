Feature Engineering Pipeline Summary Report with
key transformations and reson for decision
This report summarizes the data preprocessing, feature engineering, and feature
selection pipeline applied to the dataset, highlighting key transformations and the
rationale behind major decisions.
1. Data Cleaning & Preprocessing
Missing Values Handling:
age (numerical) and credit_score (numerical) were imputed using
the median of their respective columns. This approach is robust to outliers
and preserves the distribution shape better than mean imputation.
employment_type (categorical) was imputed using the mode (most
frequent category). This is a standard and effective strategy for
categorical data, maintaining the feature's original distribution.
Outlier Treatment:
Outliers in all numeric features were addressed using IQR-based
capping. This method replaces values outside Q1 - 1.5 * IQR and
Q3 + 1.5 * IQR with the respective bounds. It mitigates the impact of
extreme values without removing data points, thus preserving dataset size
and general structure.
2. Numeric Feature Engineering
Several transformations were applied to numerical features to improve their utility for
modeling:
Discretization (Binning) - age :
Transformation: age was binned into 5 equal-width categories, creating
age_binned.
Reasoning: To capture potential non-linear relationships with the target,
reduce noise, improve interpretability (e.g., age groups), and be
compatible with algorithms that benefit from categorical inputs.
Binarization - income :
Transformation: income was binarized into high_income (1 if income
> median, 0 otherwise).
https://colab.research.google.com/?authuser=2#scrollTo=8e788d85&printMode=true 1/5
04/04/2026, 15:16 Welcome to Colab - Colab
Reasoning: To simplify the feature, highlight a critical threshold (median
income), and create an interpretable categorical feature for certain
models.
Normalization (Min-Max Scaling) - loan_amount :
Transformation: loan_amount was scaled to a 0-1 range, creating
loan_amount_scaled.
Reasoning: To ensure all features contribute equally to distance-based
algorithms, prevent features with larger ranges from dominating, and
improve convergence for gradient-based models.
Standardization (Z-score Scaling) - credit_score :
Transformation: credit_score was scaled to have a mean of 0 and a
standard deviation of 1, creating credit_score_scaled.
Reasoning: To meet the assumptions of models that expect normally
distributed data (e.g., Logistic Regression, SVMs) and to ensure equal
feature contribution based on their spread.
Robust Scaling - monthly_spend :
Transformation: monthly_spend was scaled using its median and IQR,
creating monthly_spend_scaled.
Reasoning: To provide a scaling robust to any remaining outliers, as it
uses median and IQR instead of mean/std or min/max values, which are
more susceptible to extreme values.
Log Transformation - income :
Transformation: income was natural log-transformed, creating
income_log.
Reasoning: To reduce the right-skewness observed in the original
income distribution, making it more symmetrical and closer to a normal
distribution, which often improves model performance and satisfies
statistical assumptions.
3. Categorical Feature Engineering
One-Hot Encoding - employment_type, region :
Transformation: Nominal categorical features employment_type and
region were converted into multiple binary (0 or 1) columns.
Reasoning: To avoid implying an artificial ordinal relationship between
categories that do not have one and to make them compatible with
machine learning algorithms that require numerical input.
https://colab.research.google.com/?authuser=2#scrollTo=8e788d85&printMode=true 2/5
04/04/2026, 15:16 Welcome to Colab - Colab
Label Encoding - education_level, risk_tolerance :
Transformation: Ordinal categorical features education_level (High
School < Bachelor's < Master's < PhD) and risk_tolerance (Low <
Medium < High) were mapped to integer values (0, 1, 2, 3 and 0, 1, 2
respectively).
Reasoning: To preserve the inherent order within these features, allowing
models to leverage this ordinal information, and to simplify representation
compared to one-hot encoding for ordered categories.
4. Feature Construction
New features were created by combining existing ones based on domain knowledge
or to capture more complex relationships:
Interaction Feature - income_credit_score_interaction :
Construction: income * credit_score.
Reasoning: To capture synergistic effects where the combined impact of
income and credit score on loan default might be non-additive. A high
income with a high credit score likely indicates significantly lower risk than
their individual effects.
Ratio Feature - spending_to_income_ratio :
Construction: monthly_spend / income.
Reasoning: To provide a direct measure of financial strain, indicating how
much of an individual's income is consumed by monthly spending. Higher
ratios intuitively suggest higher risk.
Polynomial Feature - age_squared :
Construction: age**2.
Reasoning: To model non-linear relationships, such as a U-shaped or
inverted U-shaped effect of age on financial risk, where both very young
and very old individuals might have different risk profiles compared to
middle-aged groups.
Domain-Based Feature - debt_to_capacity_ratio :
Construction: (loan_amount + monthly_spend) /
financial_capacity_index.
Reasoning: To leverage domain expertise by combining total financial
obligations ( loan_amount + monthly_spend ) with a comprehensive
financial capacity measure, providing a nuanced view of an individual's
financial resilience and potential default risk.
https://colab.research.google.com/?authuser=2#scrollTo=8e788d85&printMode=true 3/5
04/04/2026, 15:16 Welcome to Colab - Colab
5. Feature Selection & Ranking
Filter-based methods were used to assess feature relevance:
Pearson Correlation: Measured linear relationships between numerical
features and loan_default (treated as quantitative).
Key Findings: debt_to_capacity_ratio, income_log,
high_income, financial_capacity_index, and loan_amount
showed the strongest linear correlations.
Chi-Square Test: Assessed the association between categorical features
(including encoded ones) and loan_default.
Key Findings: Generally showed weaker statistical associations for
individual categorical features compared to the predictive power of
numerical/engineered features.
Fisher Score ( f_classif ): Measured the discriminative power of features to
distinguish between default classes.
Key Findings: Results largely aligned with Pearson Correlation, reinforcing
the importance of financial indicators like debt_to_capacity_ratio,
income_log, and financial_capacity_index.
Multicollinearity Analysis & Retention Strategy
Highly correlated pairs were identified (e.g., financial_capacity_index and
household_cashflow_score with 0.962 correlation, income with
financial_capacity_index at 0.874). The strategy was to retain more
comprehensive or interpretable features:
financial_capacity_index was prioritized over
household_cashflow_score due to its broader assessment of financial
ability.
income_log was retained alongside financial_capacity_index due to
its fundamental interpretability and strong predictive power, balancing
multicollinearity concerns with feature importance.
6. Dimensionality Reduction (t-SNE)
Method: t-SNE was applied to the processed feature set.
Components Retained: 2 components were retained for 2D visualization
( n_components=2 ).
Information Preserved: t-SNE primarily preserves the local structure of the
data, meaning points close in high-dimensional space remain close in the
https://colab.research.google.com/?authuser=2#scrollTo=8e788d85&printMode=true 4/5
04/04/2026, 15:16 Welcome to Colab - Colab
reduced 2D space. It is used to visualize complex data structures and identify
clusters, allowing for observation of separability between defaulting and non-
defaulting individuals.
