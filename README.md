# 📈 Demand Forecasting Pipeline & Model Evaluation (H2/2022)

An end-to-end Time-Series Demand Forecasting project utilizing expanding-window Cross-Validation (CV), iterative feature engineering, and multiple machine learning benchmarks.

---

## 📌 Executive Summary

* **Forecast Horizon:** 28 days (direct multi-step forecasting).
* **Backtest Evaluation Period:** H2/2022 (184 days: July 1, 2022 – December 31, 2022).
* **Validation Strategy:** 3-Fold Expanding Window Cross-Validation with a 31-day buffer to prevent data leakage.
* **Selected Baseline:** **SPLY $y[d-364]$** (Same Period Last Year), achieving **WAPE = 34.77% (CV)** and **29.41% (Test)**.
* **Best Model Performance:** **LightGBM / GradientBoosting** with Full Feature Set, achieving **WAPE = 19.61% (Test)** and **$R^2$ = 0.6713**, reducing forecast error by **33.31%** over the selected baseline.

---

## ⚙️ Validation Strategy & Data Leakage Prevention

To ensure robust evaluation without data leakage across the 28-day forecast horizon, a **3-Fold Expanding Window** strategy with a **31-day buffer window** was implemented:

* **Fold 1:** Train up to `2019-05-31` $\rightarrow$ Valid: `2019-07-01` to `2019-12-31`
* **Fold 2:** Train up to `2020-05-31` $\rightarrow$ Valid: `2020-07-01` to `2020-12-31`
* **Fold 3:** Train up to `2021-05-31` $\rightarrow$ Valid: `2021-07-01` to `2021-12-31`
* **TEST Set:** Train up to `2022-05-31` $\rightarrow$ Test: `2022-07-01` to `2022-12-31`

---

## 🎯 Baseline Benchmarking & "Baseline Trap" Analysis

Seven heuristic baseline models were evaluated across CV folds. 

* **Chosen Baseline:** **SPLY $y[d-364]$** was selected as the legitimate baseline based on Cross-Validation (**CV WAPE = 34.77%**).
* **The "Baseline Trap" Alert:** While *2-Year SPLY Average* yielded a lower Test WAPE (24.28%), it exhibited high CV variance (CV WAPE = 39.63%). Choosing baseline based on Test set metrics introduces lookahead bias ("Baseline Trap"). Thus, **SPLY $y[d-364]$** was strictly retained as the true CV-selected benchmark (**Test WAPE = 29.41%**).

---

## 📊 Model Performance Comparison

Models were evaluated across 3-fold CV and tested on H2/2022.

| Model | CV WAPE | CV Bias | Test WAPE | Test $R^2$ | Status |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **LightGBM (Full Features)** | 0.2484 | -0.0639 | **0.1961** | **0.6713** | 🏆 **Best Test Model** |
| **Gradient Boosting** | **0.2375** | -0.0812 | 0.2233 | 0.5839 | Best CV Model |
| **XGBoost** | 0.2385 | -0.0579 | 0.2137 | 0.6302 | Candidate |
| **Random Forest** | 0.2501 | -0.0656 | 0.2109 | 0.6182 | Candidate |
| **Decision Tree** | 0.3153 | -0.0899 | 0.2535 | 0.4731 | Weak Baseline |
| *SPLY Baseline ($y[d-364]$)* | *0.3477* | *-* | *0.2941* | *-* | *Selected Benchmark* |

---

## 📈 Feature Engineering Progression

Adding domain-specific features incrementally yielded a **34.42% error reduction** over raw lag features alone:

1. **Set A (Raw 2 Lags):** CV WAPE = `37.88%` | Test WAPE = `30.22%`
2. **Set B (8 Raw Lags):** CV WAPE = `31.93%` | Test WAPE = `23.53%`
3. **Set C (Rolling Stats & Trends):** CV WAPE = `28.59%` | Test WAPE = `27.11%`
4. **Set D (+ Calendar Signals & Fourier Terms):** CV WAPE = `26.35%` | Test WAPE = `20.82%`
5. **Set E (Full Set + Holidays & Promotions):** **CV WAPE = `24.84%` | Test WAPE = `19.61%`**

---

## 🛠 Tech Stack

* **Language:** Python 3.x
* **Machine Learning:** LightGBM, XGBoost, Scikit-Learn
* **Data Processing & Feature Engineering:** Pandas, NumPy, SciPy (Fourier terms)
