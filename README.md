# Housing Insurance Claim Prediction

Academic Data Mining / Machine Learning project for **supervised binary classification**. The objective is to predict whether an insured building has at least one claim during its coverage period.

The repository preserves the original experiments and known limitations. Its architecture combines a notebook, reusable Python modules, and a CLI. It contains no deployed application or validated insurance-pricing system.

## Dataset

The project uses a housing-insurance Excel workbook with predefined `train` and `test` partitions: 5,014 raw training rows and 2,147 test rows. Two training rows are entirely empty, leaving 5,012 usable training records.

**The raw dataset is not distributed in this public repository.** Redistribution authorization is not confirmed. The academic assignment PDF is also excluded because its redistribution rights are not established.

Expected local workbook: `insurance.xlsx`. Users must provide an authorized copy separately to reproduce the complete analysis.

Target: `claim`, indicating whether at least one claim occurred during the insurance coverage period. Original labels `non` and `oui` are encoded as 0 and 1.

The nine predictors are `year`, `coverage_period`, `is_residential`, `is_finished_and_fenced`, `has_garden`, `locality`, `area_m2`, `structure_type`, and `window_count`. Identifier, geographic-code, and risk-score columns are dropped before modeling.

### Local dataset placement

- CLI: place the workbook at `data/insurance.xlsx` (create this local directory), or at the repository root as `insurance.xlsx`. Alternatively, supply `--data-path`.
- Notebook: place it at `notebooks/insurance.xlsx` and start Jupyter from `notebooks/`, because loading uses the working directory.
- These local workbook paths are ignored by Git. Do not publish the dataset without confirmed redistribution authorization.

## Data Preparation and Exploratory Analysis

Implemented preprocessing includes empty-row/column removal, conditional imputation, `RobustScaler` for area, window-count conversion, year discretization, ordinal encoding, and variance analysis with threshold 0.001. The notebook also removes duplicate rows; the reusable preprocessor does not.

The notebook computes variance-filtered matrices but trains its main models from the transformed DataFrames. Historical output retained all nine predictors. The reusable module applies variance filtering to returned matrices.

EDA covers dataset structure, descriptive statistics, missingness, duplicates, class balance, categorical distributions, area by residential status, year distributions, and correlations. Model diagnostics include confusion matrices, ROC curves, tree importance charts, and one boosting-tree visualization.

No screenshots or exported charts are included. Stored execution outputs have been cleared for privacy without changing code or Markdown.

## Modeling

The estimator families are **Random Forest** and **Gradient Boosting**. Additional notebook experiments include:

- Random Forest grid search with five-fold cross-validation and ROC-AUC scoring.
- Random Forest using the five highest-ranked predictors.
- SMOTE-balanced Random Forest and decision-threshold experiments.
- Sigmoid probability calibration of Random Forest.
- Gradient Boosting with SMOTETomek and decision-threshold experiments.

The CLI evaluates baseline RF, RF with previously selected settings, and GB with SMOTETomek. RF grid search runs only with `--tune-rf`. No fitted model artifact is included.

## Evaluation

Metrics include accuracy, precision, recall, F1, ROC-AUC, specificity, confusion matrices, and classification reports. Comparison-table precision, recall, and F1 concern the positive claim class.

The workbook supplies an approximately 70/30 train/test partition. Its original splitting procedure is undocumented. No separate validation holdout exists.

## Historical Notebook Results

These aggregate values were transcribed from historical notebook outputs inspected before cleanup. Those outputs are now cleared; model code and the existing benchmark CSV remain unchanged.

| Configuration | ROC-AUC | Claim precision | Claim recall | Claim F1 |
|---|---:|---:|---:|---:|
| Baseline Random Forest | 0.6584 | 0.4136 | 0.3110 | 0.3551 |
| Optimized Random Forest | 0.7146 | 0.6049 | 0.0965 | 0.1664 |
| Top-five Random Forest | 0.7146 | 0.6263 | 0.1220 | 0.2043 |
| Random Forest + SMOTE | 0.7043 | 0.3979 | 0.5906 | 0.4754 |
| Calibrated Random Forest | 0.7158 | 0.6154 | 0.1575 | 0.2508 |
| Gradient Boosting + SMOTETomek | 0.7112 | 0.4049 | 0.5827 | 0.4778 |

**Results were not reproduced during repository cleanup. Threshold experiments used test labels, and different configurations optimize different metrics.** These values do not establish a universally preferred model or unbiased threshold-tuned test performance.

The historical GB notebook threshold was 0.47. GB values differ from `benchmark_results.csv`, which records ROC-AUC 0.710180 and claim F1 0.481422. That CSV contains only aggregate metrics and confusion-matrix counts. Its numbers remain unchanged; its execution provenance is undocumented.

## Technology Stack

Python, pandas, NumPy, scikit-learn, imbalanced-learn, Matplotlib, Seaborn, openpyxl, and Jupyter. Requirements also declare SciPy and ipykernel.

Notebook metadata records Python 3.12.4. Requirements use minimum versions rather than exact pins. Dependencies were not changed or installed during cleanup.

## Project Structure

```text
housing-insurance-claim-prediction/
|-- notebooks/
|   `-- housing_insurance_claim_prediction.ipynb
|-- src/
|   |-- __init__.py
|   |-- data_preprocessing.py
|   |-- train.py
|   `-- evaluate.py
|-- run_pipeline.py
|-- benchmark_results.csv
|-- requirements.txt
|-- .gitignore
`-- README.md
```

Dataset files and the academic PDF are intentionally absent.

## Installation

For future local execution, create an isolated environment:

```bash
python -m venv .venv
```

Activate it in Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

Or on Linux/macOS:

```bash
source .venv/bin/activate
```

Install the unchanged declared dependencies:

```bash
python -m pip install -r requirements.txt
```

## Usage

Supply an authorized dataset before running either workflow.

```bash
python run_pipeline.py
python run_pipeline.py --data-path /path/to/insurance.xlsx
```

Optional RF grid search:

```bash
python run_pipeline.py --tune-rf
```

These commands train models and overwrite `benchmark_results.csv`. They were not executed during cleanup. If a requested dataset path is missing, the existing runner may fall back to the root workbook.

For the notebook, provide `notebooks/insurance.xlsx`, then run:

```bash
cd notebooks
jupyter notebook housing_insurance_claim_prediction.ipynb
```

Executing cells includes training and grid search. The published notebook opens without stored execution outputs.

## Limitations and Reproducibility

The original methodology is deliberately preserved:

- Threshold selection uses test labels.
- Preprocessing is fitted outside CV folds.
- No separate validation holdout exists.
- Garden imputation inserts digit `0`, while existing data uses letter `O`.
- Ordinary SMOTE interpolates ordinal categorical values.
- GB notebook and CSV results differ.
- One notebook comparison calculates AUC from binary predictions instead of continuous probabilities.
- Historical RF search output reported 216 candidates; current source defines 648.
- Some historical outputs reflect out-of-order execution, including an area chart labeled in square metres after scaling.
- Tree feature importance does not establish causal risk factors or percentages of explained risk.
- Dependencies are not locked, the dataset must be supplied separately, and no automated test suite or deployment exists.

Complete result reproduction is not guaranteed. Cleanup did not fix methodology, rerun cells, retrain models, or regenerate metrics.

## Academic Context

The supplied assignment described housing-insurance claim prediction for the Fouille de données course at Institut International de Technologie, Department of Informatics, academic year 2025-2026, semester 1. Its instructors were Taoufik Ben Abdallah and Tarek Ben Said.


## Author

**Bassem Wali**

Computer Engineering Student

GitHub: https://github.com/bassem2002

