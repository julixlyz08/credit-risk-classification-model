# Credit Risk Classification Model

## Project overview

This project develops a machine-learning model that classifies Lending Club loan applicants into two groups:

- **Prime:** grades A–C
- **Higher risk:** grades D–G

The model uses information available at the time of application, such as income, debt-to-income ratio, credit history, loan amount, loan term, and loan purpose. The goal is to provide a second-opinion risk signal that helps a lending team identify applications that may require additional review.

The project focuses on model comparison, data leakage prevention, feature engineering, and business costs. It does not predict loan default directly and should not be used as an automated lending decision without additional validation.

## Business question

Can application-time borrower information predict whether Lending Club will assign a higher-risk grade, and which classification model provides the most reliable second-opinion signal?

## Dataset

The dataset contains 10,000 Lending Club loan records from early 2018. The target variable is engineered from the original `grade` column:

```text
risky_grade = 1 if grade is D, E, F, or G
risky_grade = 0 if grade is A, B, or C
```

The dataset is imbalanced. Approximately 81.5% of loans are prime and 18.5% are classified as higher risk. Because of this imbalance, accuracy alone is not sufficient for evaluating the models.

The raw borrower-level dataset is not included in the public repository. See `data/README.md` for information about the data source and setup requirements.

## Modeling workflow

1. Load and inspect the raw loan data.
2. Create the binary `risky_grade` target.
3. Remove data-leakage and post-origination variables.
4. Handle missing values and high-cardinality fields.
5. Create additional features:
   - `loan_to_income`
   - `credit_history_years`
   - `credit_utilization_rate`
6. Split the data into training and test sets using an 80/20 stratified split.
7. Impute, scale, and encode features inside scikit-learn pipelines.
8. Train and compare four classification models.
9. Evaluate performance using accuracy, precision, recall, F1, and ROC-AUC.
10. Select a classification threshold using an asymmetric business-cost analysis.

## Data leakage prevention

Several fields were excluded because they either reveal the target directly or would not be available when an application is evaluated. These include:

- `grade` and `sub_grade`
- `interest_rate` and `installment`
- `loan_status`
- payment and balance fields recorded after origination
- other approval or post-origination fields

Removing these variables helps make the evaluation more realistic and prevents the model from learning information it would not have at prediction time.

## Models compared

- Logistic Regression
- Decision Tree
- Neural Network
- Random Forest

Each model uses the same preprocessing approach so the comparison is consistent. The preprocessing pipeline includes median imputation for numeric variables, most-frequent imputation for categorical variables, standardization of numeric variables, and one-hot encoding of categorical variables.

## Results

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Logistic Regression | 0.8240 | 0.5634 | 0.2162 | 0.3125 | 0.7711 |
| Decision Tree | 0.8070 | 0.4403 | 0.1595 | 0.2341 | 0.7219 |
| Neural Network | 0.7835 | 0.3994 | 0.3378 | 0.3660 | 0.7112 |
| Random Forest | 0.8270 | 0.6765 | 0.1243 | 0.2100 | 0.7877 |

### Recommended model

Logistic Regression is recommended as the second-opinion model because it generalizes more consistently from the training data to the test data. It achieved the best test F1 score and test recall among the four models, while the Neural Network and Random Forest showed substantially larger train-to-test performance gaps.

Random Forest achieved the highest test ROC-AUC, but its test F1 and recall were lower than Logistic Regression. For this business problem, identifying more higher-risk applications and maintaining reliable performance on unseen data are more important than maximizing one threshold-independent ranking metric.

## Business-cost analysis

Missing a higher-risk borrower is assumed to cost approximately 10 times more than incorrectly flagging a prime borrower. The threshold analysis therefore uses:

```text
Total cost = false positives × 1 + false negatives × 10
```

At the default 0.50 threshold:

- Recall: 21.6%
- False negatives: 290
- Estimated cost: 2,962 units

At the selected 0.10 threshold:

- Recall: 85.9%
- Precision: 27.4%
- False negatives: 52
- Estimated cost: 1,362 units

The recommended operating approach is to use the Logistic Regression model as a prescreening tool. Applications above the 0.10 probability threshold should receive manual review rather than automatic rejection. This threshold should be recalibrated with current data and verified business costs before any operational use.

## Most influential features

The five features with the largest absolute standardized Logistic Regression coefficients were:

1. `term`
2. `verified_income_Verified`
3. `total_debit_limit`
4. `num_open_cc_accounts`
5. `credit_utilization_rate`

These features describe loan duration, income verification, available credit, open credit accounts, and the proportion of available credit currently being used. Feature importance indicates association with the model output, not causation.

## Repository structure

```text
credit-risk-classification-model/
├── README.md
├── credit_risk_classification.ipynb
├── requirements.txt
├── data/
│   └── README.md
└── docs/
    └── memo.pdf
```

## How to run the project

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/credit-risk-classification-model.git
cd credit-risk-classification-model
```

### 2. Install the dependencies

```bash
pip install -r requirements.txt
```

### 3. Add the dataset

Place the dataset at:

```text
data/lending_club_raw.csv
```

The notebook expects the CSV to be located in the `data` folder.

### 4. Launch the notebook

```bash
jupyter notebook credit_risk_classification.ipynb
```

Run the notebook from beginning to end to reproduce the exploratory analysis, preprocessing, model comparison, threshold analysis, and feature interpretation.

## Limitations

- The dataset represents loans from early 2018 and may not reflect current borrower behavior or economic conditions.
- The target represents Lending Club's assigned grade category, not actual loan default.
- The model uses a single train/test split rather than a full production validation process.
- The 0.10 threshold depends on the assumed 10:1 cost ratio and should be recalibrated with verified business costs.
- The model should support human review rather than make automatic lending decisions.
- Fair-lending, explainability, stability, and regulatory validation would be required before deployment.

## Future improvements

- Validate the model on a later time period to measure performance drift.
- Add cross-validation and probability calibration.
- Evaluate fairness across relevant borrower groups.
- Monitor recall, precision, and cost after deployment.
- Compare the model with a current lending benchmark.
- Build an interactive monitoring dashboard.

## Author

Julie Loyez  
MS in Business Analytics student
