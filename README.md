# Investigating associations between mitochondrial DNA heteroplasmy variants and adverse birth outcomes across South Asian and African populations.

## Overview
This repository contains the analysis pipeline for studying whether mitochondrial DNA (mtDNA) heteroplasmy variants can predict preterm birth (PTB) and gestational age (GA) outcomes. Analyses are conducted both across all populations combined and stratified by ancestry group (South Asian and African).

## Background
mtDNA heteroplasmy is the coexistence of more than one mtDNA variant within a cell and it has been implicated in a range of complex traits and diseases. This project examines whether heteroplasmic variants captured in the dataset are predictive of:

Preterm birth (PTB): binary outcome (yes/no)
Gestational age at birth (GAGEBRTH): continuous outcome (days)

Covariates include maternal age (PW_AGE), body mass index (BMI), and study site. Population stratification is handled by running analyses separately for South Asian (GAPPS-Bangladesh, AMANHI-Pakistan, AMANHI-Bangladesh) and African (AMANHI-Pemba, GAPPS-Zambia) cohorts.

## Repository Structure
mtDNA-heteroplasmy-preterm-birth/
```
│
├── notebooks/
│   ├── Heteroplasmy_Analysis_for_all_populations.ipynb   # Combined analysis
│   └── Heteroplasmy_analysis_for_populations_separately.ipynb  # Stratified by ancestry
│
├── figures/                        # Output plots from analyses
│
├── requirements.txt                # Python dependencies
└── README.md
```

## Analysis
Two complementary feature sets are used in each analysis:
Two complementary feature sets are used in each analysis:
 
| Feature Set | Description |
|---|---|
| **mtDNA PCs** | PCA-reduced representation of variant matrix (80% variance retained) |
| **All Variants** | Direct use of all `Var_` columns as predictors |
 
Each feature set is tested against both outcomes using three models:
 
| Model | PTB (binary) | Gestational Age (continuous) |
|---|---|---|
| Regression | Logistic Regression + FDR correction | OLS Linear Regression + FDR correction |
| Mixed Effects | Mixed LM with site as random effect | Mixed LM with site as random effect |
| Machine Learning | Random Forest Classifier + SHAP | Random Forest Regressor + SHAP |

## Notebooks
### `Heteroplasmy_Analysis_for_all_populations.ipynb`
Runs the full analysis pipeline on the combined South Asian + African dataset. Site is included as a fixed-effect covariate (one-hot encoded) or as a random effect in mixed models.
 
**Key findings:**
- Site-level effects dominate both PTB and GA outcomes (e.g., GAPPS-Bangladesh shows ~2.6× higher PTB risk)
- No individual mtDNA variants survive FDR correction (p_adj < 0.05)
- mtDNA PC1, PC5, PC11, and PC14 show nominal significance for PTB in the mixed-effects model
- Random forest predictive performance is modest: PTB AUC ≈ 0.62, GA R² ≈ 0.04
### `Heteroplasmy_analysis_for_populations_separately.ipynb`
Stratifies the sample by ancestry group and re-runs the same pipeline for each population independently.
 
- **South Asian sites:** GAPPS-Bangladesh, AMANHI-Pakistan, AMANHI-Bangladesh
- **African sites:** AMANHI-Pemba, GAPPS-Zambia

## Dependencies
 
```
pandas
numpy
scikit-learn
statsmodels
shap
matplotlib
```
 
Install all dependencies with:
 
```bash
pip install -r requirements.txt
```
 
> **Note:** Notebooks were developed in Google Colab. The data loading step uses `google.colab.drive` — replace the data path with your local path if running outside Colab.

## Data
 
The input dataset (`HeteroplasmyCalls.csv`) contains:
 
| Column | Description |
|---|---|
| `Var_*` | Heteroplasmy variant calls (one column per variant) |
| `PTB` | Preterm birth indicator (binary) |
| `GAGEBRTH` | Gestational age at birth (days) |
| `PW_AGE` | Maternal age at enrollment |
| `BMI` | Pre-pregnancy BMI |
| `MainHap` | Mitochondrial haplogroup |
| `population` | Ancestry group |
| `site` | Study site |
 
> ⚠️ Raw data is not included in this repository due to data use agreements.

## Statistical Notes
 
- Multiple testing correction uses the **Benjamini-Hochberg FDR** procedure (threshold: p_adj < 0.05)
- PCA retains components explaining **80% of variance** in the variant matrix
- Random Forest models use 5-fold cross-validation; class imbalance for PTB is handled with `class_weight='balanced'`
- SHAP values are computed using `TreeExplainer` for model interpretability


