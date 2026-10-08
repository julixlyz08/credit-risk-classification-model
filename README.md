# Dataset: Lending Club loans (early 2018)

## File

`lending_club_raw.csv`: 10,000 rows (one per loan) and 55 columns.

## Source

Loan data from Lending Club, a peer-to-peer lending platform, for loans issued from January to March 2018. This file matches the public `loans_full_schema` dataset distributed by OpenIntro:
https://www.openintro.org/data/index.php?data=loans_full_schema

OpenIntro datasets are shared under the Creative Commons Attribution-ShareAlike 3.0 license.

## What the columns contain

| Group | Example columns |
|---|---|
| Applicant | `emp_title`, `emp_length`, `state`, `homeownership`, `annual_income`, `verified_income`, `debt_to_income` |
| Joint applications | `annual_income_joint`, `verification_income_joint`, `debt_to_income_joint` |
| Credit history | `earliest_credit_line`, `delinq_2y`, `inquiries_last_12m`, `total_credit_limit`, `total_debit_limit`, `num_open_cc_accounts` |
| Loan request | `loan_purpose`, `application_type`, `loan_amount`, `term` |
| Lending Club decision | `grade`, `sub_grade`, `interest_rate`, `installment` |
| After the loan was issued | `issue_month`, `loan_status`, `balance`, `paid_total`, `paid_principal`, `paid_interest`, `paid_late_fees` |

## Target variable

The model predicts whether Lending Club assigned a higher-risk grade:

```text
risky_grade = 1 if grade is D, E, F, or G   (1,851 loans, 18.5%)
risky_grade = 0 if grade is A, B, or C      (8,149 loans, 81.5%)
```

## Columns excluded from the model

To avoid data leakage, the model only uses information available when someone applies. The notebook removes:

- `grade` and `sub_grade` (they define the target)
- `interest_rate` and `installment` (set by Lending Club based on the grade)
- `loan_status`, `balance`, and the `paid_*` columns (only known after the loan is issued)

## Notes

- Loans are from early 2018, so patterns may not reflect current borrowers or economic conditions.
- `grade` is Lending Club's own risk rating, not an actual default outcome.
