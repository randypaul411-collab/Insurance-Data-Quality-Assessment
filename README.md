#  Insurance Data Quality Analysis
### A Production-Style Data Quality Project Using Great Expectations

![Python](https://img.shields.io/badge/Python-3.12-blue)
![Great Expectations](https://img.shields.io/badge/Great%20Expectations-1.18.0-orange)
![Pandas](https://img.shields.io/badge/Pandas-2.x-green)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## Project Overview

This project demonstrates a professional **Data Quality Assessment** on a real insurance
covered lives dataset using **Great Expectations (GE) 1.18**. It simulates the kind of
DQ pipeline a Data Quality Analyst would build in a regulated insurance or banking
environment — covering all four major data quality dimensions, generating automated
reports, and producing an executive-level scorecard.

---

## Dataset

| Attribute     | Detail                          |
|---------------|---------------------------------|
| Domain        | Life Insurance                  |
| Records       | 8,359 covered life records      |
| Columns       | 48 (financial, demographic, policy, status) |
| Key Fields    | Policy_Number, Covered_Life_ID, Premium_Amount, Policy_Status, Issue_Date, Maturity_Date, Term |

---

## Data Quality Dimensions Assessed

| Dimension        | Checks | Score | Grade  |
|------------------|--------|-------|--------|
| ✅ Completeness  | 18     | 88.9% | 🟡 FAIR |
| ✅ Validity      | 14     | 64.3% | 🔴 POOR |
| ✅ Consistency   | 8      | 37.5% | 🔴 POOR |
| ✅ Freshness     | 4      | 75.0% | 🟡 FAIR |
| **Overall DQ Score** | **44** | **66.4%** | 🔴 **POOR** |

> A low DQ score is not a project failure — it means the validation framework is
> working correctly and surfacing real issues that exist in the data.
> 
### Validation Preview

![Validation Result](image/validation_result.png) 

---
## Key Findings

### Completeness
- **6,234 records (74.58%)** missing values in a critical column — highest priority fix
- **12 records (0.14%)** missing values in a secondary column

### Validity
- **`Policy_Status`** — 32 records (0.38%) contain invalid values:
  - `ACTIVEE` (19 records) — data entry typo, should be `ACTIVE`
  - `LAPSED` (13 records) — inconsistent with the standard value `LAPSE`
- **`Premium_Amount`** — 83 records (0.99%) with invalid values:
  - Negative premiums (e.g. `-50.0`) — likely system reversal errors
  - Zero premiums (`0.0`) — should not exist for active policies
- **`Covered_Life_ID`** — 166 duplicate IDs (1.99%) violating uniqueness constraint
- **`Term` column** — confirmed to be in **months** (range: 12–456), not years

### Consistency (Cross-Column Business Rules)
- **`Maturity_Date > Issue_Date`** — 61 violations (0.73%): maturity before issue
- **`Last_Paid_Date >= Issue_Date`** — 31 violations (0.37%): payment before policy start
- **`Date_of_Purchase >= Issue_Date`** — 1,030 violations (12.32%): purchase before issue
- **`Tenure_Years <= Term`** — 3,508 violations (41.97%): tenure exceeds policy term
- **`Maturity_Date ≈ Issue_Date + Term`** — 1,071 violations (12.81%): date mismatch

### Freshness
- **`Date_of_Purchase`** — 2,125 records (25.42%) are future-dated

---

## Tools & Technologies

| Tool | Purpose |
|------|---------|
| **Great Expectations 1.18** | Expectation suite, checkpoint, Data Docs |
| **Pandas** | Data loading, transformation, cross-column checks |
| **Jupyter Notebook** | Interactive development and documentation |
| **Python 3.12** | Core language |

---

---

##  Business Recommendations

Based on the findings, the following actions are recommended for the data steward
and source system owners:

1. **Completeness** — Add `NOT NULL` constraints on critical fields at the source
   system level; implement mandatory field validation at point of entry
2. **Policy_Status** — Standardize to a controlled enum `[ACTIVE, LAPSE, NEVER LAPSE,
   SURRENDERED, MATURED]`; add validation at ETL layer
3. **Premium_Amount** — Investigate negative and zero premium records; add
   `CHECK (premium_amount > 0)` constraint at database level
4. **Covered_Life_ID** — Enforce uniqueness at source; deduplicate existing records
5. **Date Logic** — Add cross-field validation in ETL pipeline to enforce
   `Issue_Date ≤ Date_of_Purchase ≤ Maturity_Date`
6. **Tenure vs Term** — Clarify business definition; Term is confirmed in months,
   ensure Tenure_Years conversion is applied consistently
7. **Future Dates** — Investigate source system clock/timezone issues causing
   future `Date_of_Purchase` entries

---

## Regulatory Context

This project is structured around data quality dimensions relevant to insurance
and banking regulatory frameworks:

- **IFRS 17** — Requires accurate policy and premium data for insurance contract
  measurement
- **BCBS 239** — Demands data accuracy, completeness and timeliness for risk reporting
- **OSFI (Canada)** — Expects insurers to maintain data governance frameworks with
  documented DQ controls
---

---
## Project Structure

```
insurance-dq-project/
├── data/
│   └── covered_lives.csv              ← Source dataset (8,359 records)
├── great_expectations/
│   ├── checkpoints/                   ← GE checkpoint config
│   ├── expectations/                  ← Saved expectation suites
│   └── uncommitted/
│       └── data_docs/
│           └── local_site/
│               └── index.html         ← Auto-generated HTML report
├── insurance_dq_final.ipynb           ← Main project notebook
├── requirements.txt                   ← Reproducible dependencies
└── README.md
```

---

## How to Run

```bash
# 1. Clone the repo
git clone https://github.com/YOUR_USERNAME/insurance-dq-project.git
cd insurance-dq-project

# 2. Create and activate virtual environment
python -m venv venv
source venv/bin/activate        # Mac/Linux
venv\Scripts\activate           # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. Launch Jupyter
jupyter notebook

# 5. Open and run all cells in:
#    insurance_policy_DQC.ipynb

# 6. Data Docs report opens automatically in your browser
#    Or open manually:
#    great_expectations/uncommitted/data_docs/local_site/index.html
```
---
---
- **Python Scripts**
  - [Insurance_policy_DQC.ipynb](https://github.com/randypaul411-collab/Insurance-Data-Quality-Assessment/blob/main/Insurance%20Data%20Quality%20Assessment/Insurance_policy_DQC.ipynb))
  

-  **Dataset**  
   - [insurance_policy_data.csv ](https://github.com/randypaul411-collab/Insurance-Data-Quality-Assessment/blob/main/Insurance%20Data%20Quality%20Assessment/insurance_policy_covered_lives_raw_dataset.csv)
 
   ---
