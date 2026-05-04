# Loan Approval Strategy Comparison Report: A/B-Style Policy Simulation

## Overview
This project implements a **predictive modeling framework** to compare two loan approval strategies using logistic regression. It evaluates the trade-offs between submission volume, approval rates, and risk management across different applicant segments.

## Objective
To quantitatively assess the impact of expanding loan approval eligibility from a **conservative strategy (Top 20% applicants)** to a **broader strategy (Top 40% applicants)** while monitoring key performance metrics and risk indicators.

## Dataset
- **Type:** Synthetically generated loan application records
- **Size:** 16,000 simulated applicants
- **Date Range:** 2018-01-01 to 2061-10-21 (extended range for temporal train/test separation)
- **Split:** 80% training data (early records) / 20% testing data (recent records)
- **Baseline Approval Rate:** ~24% across both train and test sets

**Note:** This is synthetic/simulated data created for model development and policy comparison analysis. The extended date range (spanning 43+ years) is a byproduct of the data generation process and is used solely to enable time-based train/test splitting without temporal leakage. Results represent theoretical scenarios rather than historical loan performance.

### Key Features (35 columns)
- **Demographics:** Age, Education Level, Marital Status, Employment Status
- **Financial:** Annual Income, Monthly Income, Net Worth, Total Liabilities
- **Credit Metrics:** Credit Score, RiskScore, Bankruptcy History
- **Loan Details:** Loan Amount, Loan Duration, Interest Rate, Monthly Payment
- **Affordability:** Total Debt-to-Income Ratio (DTI), Utility Bills Payment History, Job Tenure
- **Target:** LoanApproved (binary: 0/1)

## Methodology

### 1. **Data Preprocessing**
- Time-based train/test split to prevent temporal data leakage
- Removal of NaT (Not-a-Time) date entries
- Categorical encoding using OneHotEncoder

### 2. **Model Development**
- **Algorithm:** Logistic Regression (with 2000 max iterations)
- **Pipeline:** ColumnTransformer + Pipeline for automated preprocessing
- **Output:** Approval probability score (`p_approve`) for ranking applicants

### 3. **Policy Simulation**
Three distinct policies are evaluated:

| Policy | Description | Applicants |
|--------|-------------|-----------|
| **Policy A (Top 20%)** | Most conservative; approve highest-scored 20% | 800 |
| **Policy B (Top 40%)** | Balanced expansion; approve highest-scored 40% | 1,600 |
| **Policy C (20%-40%)** | Marginal tier; applicants ranked 20th-40th percentile | 800 |

### 4. **Payout Proxy (Risk Guardrail)**
Applicants must satisfy ALL criteria to reach final payout:
- `LoanApproved == 1` (model prediction)
- `RiskScore <= 60` (risk threshold)
- `TotalDebtToIncomeRatio <= 0.55` (affordability limit)
- `BankruptcyHistory == 0` (no prior bankruptcy)

## Key Results

### Comparative Analysis

| Metric | Policy A (Top 20%) | Policy B (Top 40%) | Policy C (20%-40%) |
|--------|-------|-------|--------|
| **Submissions** | 800 | 1,600 | 800 |
| **Grants Approved** | 740 | 942 | 202 |
| **Grant Rate** | 92.5% | 58.9% | 25.3% |
| **Payout (Proxy)** | 716 | 918 | 202 |
| **End-to-End Payout Rate** | 89.5% | 57.4% | 25.3% |
| **Avg Credit Score** | 588.90 | 585.84 | 582.79 |
| **Avg Risk Score** | 40.66 | 44.98 | 49.30 |
| **Avg DTI Ratio** | 0.147 | 0.202 | 0.257 |

### Key Insights
1. **Policy A** delivers the highest quality with 92.5% approval and 89.5% payout rate, but limits volume
2. **Policy B** increases submissions by 2x (800→1,600) but reduces approval quality (92.5%→58.9%)
3. **Policy C** (marginal applicants) has poor conversion metrics, suggesting expansion beyond top 40% is risky
4. Risk scores increase monotonically from Policy A → C, indicating quality deterioration with broader criteria

## Technical Stack
- **Language:** Python 3.12.4
- **Data Processing:** Pandas, NumPy
- **Machine Learning:** Scikit-learn (LogisticRegression, ColumnTransformer, Pipeline)
- **Development:** Jupyter Notebook

## Files
- `Loan (1).ipynb` - Main analysis notebook with full model pipeline and policy simulation
- `Loan.xls` - Source dataset (5.5 MB)
- `README.md` - Project documentation

## Usage

To run the analysis:

```bash
jupyter notebook "Loan (1).ipynb"
```

Run cells sequentially to:
1. Load and preprocess the loan data
2. Train the logistic regression model
3. Generate approval probability scores
4. Simulate three distinct approval policies
5. Compare funnel metrics and risk indicators

## Business Recommendations

- **Conservative Approach:** Policy A maximizes approval quality with minimal risk; ideal for risk-averse institutions
- **Growth-Focused:** Policy B increases volume but requires stronger fraud detection/underwriting
- **Beyond Top 40%:** Not recommended; applicants outside top 40% show poor fundamentals (low credit, high DTI)

## Future Enhancements
- Cross-validation and model performance metrics (AUC-ROC, precision-recall)
- Feature importance analysis to identify key approval drivers
- Scenario analysis with alternative thresholds (RiskScore, DTI)
- Revenue/loss simulation based on default rates by policy tier

## Author
**Shuang-Lin-Chen**  
Date: 2026-05-04

## License
Open source. See repository for details.