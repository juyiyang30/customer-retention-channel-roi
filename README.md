# Customer Retention & Channel ROI

A customer value and retention analysis for an Irish online retailer. It uses ~30K customers who registered in Q1 2013 to find which acquisition channels and customer segments drive profit, and which customers are unlikely to reorder.

## Key Questions Answered

1. **Channel Performance**: Which acquisition channels bring the most total and per-customer profit?
2. **Monthly ROI**: How does ROI move month by month for paid channels (Paid Search, Affiliates)?
3. **Segments**: How do profit and retention differ by title, city, birth decade, sign-up time and contact opt-in?
4. **Retention Gap**: How much more profit do customers who reorder generate than customers who don't?
5. **Retention Modeling**: Can we predict which customers will not place a second order, and what drives it?

## Key Findings

- **Direct** brings in the most total profit. **Paid Social** has the highest profit per customer, on both first and later orders.
- **Affiliates** ROI fell each month (1.71 → 0.62 → 0.60) and turned negative after January, so its spend should be re-evaluated.
- Male customers, customers born in the 1950s–60s, and customers who allow contact have higher average profit. **Cork** is the largest high-value market outside Dublin.
- Customers who reorder generate far more profit than one-time buyers. This gap motivated the retention model.
- Three retention models were compared on a held-out test set (class 0 = customers who did not reorder):

  | Model | ROC-AUC | Class-0 recall | Class-0 precision | Accuracy |
  |---|---|---|---|---|
  | Logistic Regression | 0.70 | 0.85 | 0.50 | 0.65 |
  | Random Forest (balanced) | 0.76 | 0.95 | 0.48 | 0.61 |
  | XGBoost (SMOTE + randomized search) | 0.83 | 0.53 | 0.69 | 0.75 |

  XGBoost ranks customers best (highest AUC). I recommended **Random Forest** at its default threshold because the business goal is to catch almost every at-risk customer, and a cheap retention offer sent to someone who would have reordered anyway costs little. The trade-off is low precision: many customers get flagged. Tuning XGBoost's decision threshold for recall is the natural next step.

## Methods & Tools

- **Python** (pandas, numpy, matplotlib, seaborn): data merging, segment metrics, distribution plots
- **Channel ROI**: monthly profit vs. acquisition cost by channel
- **Machine Learning** (scikit-learn, XGBoost, imbalanced-learn): Logistic Regression, Random Forest and XGBoost with SMOTE and a randomized search tuned for class-0 recall

## Limitations & What I'd Do Differently

- **Promotion leakage**: per the dataset definitions, `Total value of all promotions` counts promotions redeemed through June 30, including on later orders. Customers who reorder have more chances to redeem, so the model's top feature (promotions, 0.65 importance) partly reflects the outcome itself. Next time I would use only promotions on the first order, or measure features at a fixed cutoff date after registration.
- **Inconsistent profit definition**: `net_profit` subtracts promotions in some cells and not in others. I would define it once and reuse it.
- **Missing cost data**: there is no cost data for Paid Social, Direct and Organic Search, so their ROI cannot be compared directly with the paid channels.
- **Causality**: segment differences are descriptive. A test (e.g., a targeted discount A/B test) would be needed before acting on them.

## Dataset

Customer demographics, first and subsequent order profit, promotions and monthly channel costs (~30K customers, Jan–Mar 2013 registrations, orders through June 2013). Raw data not included.

## Files

- `customer_retention_analysis.ipynb`: full analysis notebook with code, tables, charts and models
- `customer_retention_report.pdf`: write-up with executive summary, findings and recommended actions
