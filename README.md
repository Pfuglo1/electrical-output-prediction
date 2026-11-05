# electrical-output-prediction

# Electrical Energy Output Prediction — Combined Cycle Power Plant
 
![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-150458?logo=pandas&logoColor=white)
![SciPy](https://img.shields.io/badge/SciPy-Statistics-8CAAE6?logo=scipy&logoColor=white)
![TensorFlow](https://img.shields.io/badge/TensorFlow-Keras-orange?logo=tensorflow&logoColor=white)
![Colab](https://img.shields.io/badge/Run%20on-Google%20Colab-F9AB00?logo=googlecolab&logoColor=white)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow)
 
An end-to-end data analysis and feature engineering pipeline for predicting the **net hourly electrical energy output (PE)** of a combined cycle power plant from four ambient conditions: temperature, exhaust vacuum, atmospheric pressure and relative humidity. The project prepares the data for a deep learning regression model built with TensorFlow/Keras.
 
---
 
## Table of Contents
 
- [Problem Statement](#problem-statement)
- [Dataset](#dataset)
- [Project Workflow](#project-workflow)
- [Key Findings](#key-findings)
- [Tech Stack](#tech-stack)
- [How to Run](#how-to-run)
- [Project Structure](#project-structure)
- [Roadmap](#roadmap)
- [Author](#author)
---
 
## Problem Statement
 
A combined cycle power plant generates electricity with gas turbines and steam turbines working together. Its output depends heavily on ambient conditions. Accurately predicting full-load output from weather variables helps operators:
 
- plan generation and grid commitments
- maximise revenue from available capacity
- detect performance drops early
This is framed as a **supervised regression** problem with `PE` (MW) as the target.
 
---
 
## Dataset
 
**Source:** [Combined Cycle Power Plant — UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/294/combined+cycle+power+plant)
 
The data was collected over six years (2006–2011) while the plant ran at full load.
 
| Column | Type | Description | Unit |
|---|---|---|---|
| `AT` | float | Ambient Temperature | °C |
| `V` | float | Exhaust Vacuum | cm Hg |
| `AP` | float | Ambient Pressure | mbar |
| `RH` | float | Relative Humidity | % |
| `PE` | float | **Target**: net hourly electrical energy output | MW |
 
**Data at a glance**
 
| Stage | Rows |
|---|---|
| All 5 Excel sheets combined | 47,840 |
| After removing duplicates | 9,527 |
| After IQR outlier removal | **9,423** |
 
> ℹ️ The original Excel file has 5 sheets that are shuffled copies of the same records. Combining them produced 38,313 duplicate rows, and removing them brought the data back to the unique observations.
 
---
 
## Project Workflow
 
### 1. Data Loading & Cleaning
- Merged the 5 Excel sheets into a single CSV.
- Confirmed there are no missing values in any column.
- Removed **38,313 duplicate rows**.
### 2. Outlier Detection (IQR Method)
| Feature | Outliers found |
|---|---|
| AT | 0 |
| V | 0 |
| AP | 91 |
| RH | 13 |
 
Removed 104 outlier rows in total.
 
### 3. Exploratory Data Analysis
- Target distribution: `PE` is close to symmetric (skew = 0.31).
- Histograms and KDE plots for every feature.
- Scatter and regression plots of each feature against `PE`.
- Correlation heatmap and feature–target correlation bar chart.
### 4. Statistical Analysis
**Correlation significance tests (Pearson & Spearman)**
 
| Feature | Pearson r | Spearman ρ | p-value | Relationship |
|---|---|---|---|---|
| AT | **−0.947** | −0.943 | < 0.001 | Strong negative |
| V | **−0.869** | −0.884 | < 0.001 | Strong negative |
| AP | +0.522 | +0.542 | < 0.001 | Moderate positive |
| RH | +0.389 | +0.390 | < 0.001 | Weak–moderate positive |
 
All four features have a statistically significant relationship with energy output.
 
**Normality testing (Shapiro–Wilk)**
- None of the four features is normally distributed (all p < 0.05).
- A **Yeo-Johnson power transform** was applied to bring the features closer to normal.
### 5. Feature Engineering
Four interaction features were created to help the model learn combined environmental effects:
 
| Feature | Formula | Intuition |
|---|---|---|
| `AT_RH_Interaction` | AT × RH | Combined thermal–moisture effect |
| `V_AP_Diff` | V − AP | Vacuum vs. atmospheric pressure imbalance |
| `Thermal_Efficiency_Index` | AT / (V + 1) | Temperature relative to condenser vacuum |
| `Environmental_Stress` | (AT + RH + V) / 3 | Aggregate environmental load |
 
---
 
## Key Findings
 
- 🌡️ **Ambient temperature is the dominant driver.** Output falls sharply as temperature rises (r = −0.95), consistent with gas turbine physics: warmer air is less dense, so less mass flows through the turbine.
- 💨 **Exhaust vacuum** is the second strongest predictor (r = −0.87). It is also highly correlated with temperature.
- 📈 **Pressure and humidity** have smaller but statistically significant positive effects.
- The strong, near-linear relationships suggest that even simple models will perform well, so the deep learning model has to beat a linear baseline to justify itself.
---
 
## Tech Stack
 
| Category | Tools |
|---|---|
| Language | Python |
| Data handling | Pandas, NumPy |
| Visualisation | Matplotlib, Seaborn |
| Statistics | SciPy (`pearsonr`, `spearmanr`, `shapiro`) |
| Preprocessing | scikit-learn (`PowerTransformer`, `StandardScaler`) |
| Deep learning | TensorFlow / Keras |
| Environment | Google Colab |
 
---
 
## How to Run
 
### Option 1: Google Colab (recommended)
1. Download the dataset from the [UCI repository](https://archive.ics.uci.edu/dataset/294/combined+cycle+power+plant).
2. Combine the Excel sheets into `combined_electrical_op_prediction.csv` (or use just the first sheet, since all five hold the same records).
3. Open the notebook in [Google Colab](https://colab.research.google.com/) and upload the CSV to `/content/`.
4. Run all cells.
### Option 2: Run locally
```bash
git clone https://github.com/<your-username>/electrical-output-prediction.git
cd electrical-output-prediction
pip install -r requirements.txt
jupyter notebook
```
Then update the `path` variable in the first code cell to point to your local copy of the CSV.
 
---
 
## Project Structure
 
```
electrical-output-prediction/
├── Electrical_Output_Prediction.ipynb   # EDA, statistics and feature engineering
├── requirements.txt                     # Python dependencies
└── README.md                            # Project documentation
```
 
---
 
## Roadmap
 
- [x] Data cleaning & deduplication
- [x] Outlier treatment
- [x] Exploratory data analysis
- [x] Statistical hypothesis testing
- [x] Feature transformation & engineering
- [ ] Train/test split and feature scaling
- [ ] Linear Regression baseline
- [ ] Deep neural network regressor (Keras) with early stopping
- [ ] Evaluation with MAE, RMSE and R²
- [ ] Feature importance / SHAP analysis
**Technical notes for the modelling stage**
- `AP` values (~1000 mbar) collapse to a near-constant column after the Yeo-Johnson transform. Using `StandardScaler` on `AP`, or skipping the power transform entirely (all features already have |skew| < 0.5), would keep its signal.
- `Thermal_Efficiency_Index` divides by `V + 1`, and transformed `V` can be close to −1, which produces extreme values. Computing it on the raw features would avoid that.
- Fitting the transformers on the training split only, inside a scikit-learn `Pipeline`, prevents data leakage.
---
 
## Author
 
**Patricia Fuglo**
 
⭐ If you found this project useful, consider giving it a star!
 
