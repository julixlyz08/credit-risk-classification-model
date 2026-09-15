## Project: Capstone Project

Goal: Predict when a borrower is classified as higher-risk versus prime based on application characteristics (classification).

Dataset: lending_club_raw.csv

Target variable: risky_grade = 1 if grade is in {D, E, F, G}; risky_grade = 0 if grade is in {A, B, C}.

Allowed libraries: pandas, numpy, scikit-learn, matplotlib, seaborn
Do not use: statsmodels, xgboost, tensorflow

Naming conventions: snake_case for all variables and functions
Random state: always use random_state=42

Columns to drop before modeling:
    grade, sub_grade, interest_rate, installment, loan_status, initial_listing_status, disbursement_method, balance, paid_total, paid_principal, paid_interest, paid_late_fees

Output format: 
    - results_df columns: Model, Accuracy, Precision, F1, ROC-AUC
    - All metric values rounded to 4 decimal places 
    - Train/test split: 80/20