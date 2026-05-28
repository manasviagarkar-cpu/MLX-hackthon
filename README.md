# MLX - Session Zero: Solar Radiation Prediction Challenge ☀️

This repository contains the **4th-place solution** for the **MLX - Session Zero** model-building competition, organized by the **Google Developer Group on Campus (GDGOC), Heritage Institute of Technology, Kolkata**. 

The challenge required building a highly accurate regression pipeline to estimate solar radiation levels using real-world atmospheric, temporal, and environmental sensor data collected over a four-month period.

---

## 🏆 Competition Outcomes
* **Global Leaderboard Rank:** 4th Place
* **Final Evaluation Metric (RMSE):** 6.24917
* **Objective:** Minimize the Root Mean Squared Error (RMSE) between predicted and actual continuous values of solar radiation.

---

## 🚀 Pipeline Architecture Overview

To achieve a podium-level score without overfitting to the public leaderboard, the architecture relies on heavy domain-specific feature engineering, robust cross-validation, and a gradient-boosted ensemble.

### 1. Robust Validation Strategy
* **Method:** 5-Fold Cross-Validation (Stratified by target binning to ensure stable distribution across folds).
* **Benefit:** Prevented data leakage across temporal configurations and ensured that local Out-of-Fold (OOF) validation closely matched the Kaggle hidden test set evaluation.

### 2. Advanced Feature Engineering (The Winning Edge)
Tree-based models inherently struggle with raw timestamps and circular angular bounds. The following domain-specific features unlocked significant performance gains:
* **Cyclic Time Transforms:** Decoupled `UNIXTime` into human-readable datetime features, then encoded `Hour` (24-hour cycle) and `Day of Year` (365-day cycle) into **Sine and Cosine components** to preserve temporal continuity.
* **Wind Vector Decomposition:** Converted polar coordinates (`Wind Direction` in degrees and `Wind Speed`) into Cartesian coordinates (**$U$ and $V$ trigonometric wind components**), giving decision trees true directional vector physics.
* **Meteorological Interaction Terms:** Developed atmospheric proxies including **Temperature-Humidity Ratios** and **Estimated Dew Point** equations to map cloud cover/moisture densities implicitly.

### 3. The Gradient Boosting Ensemble
Leveraged a strategic blend of the three leading gradient-boosted frameworks, optimized via localized hyperparameter tuning to handle sensor noise differently:
* **LightGBM:** Fast leaf-wise tree growth for capturing sharp non-linear interactions.
* **XGBoost:** Highly robust level-wise depth control to manage variance.
* **CatBoost:** Built-in symmetric trees optimized specifically to neutralize noisy tabular sensor inputs.

### 4. Domain-Enforced Post-Processing
* **Absolute Zero Clamping:** Solar radiation mathematically cannot fall below zero. Model predictions dipping into negative margins due to numerical fluctuations were strictly clipped to `0`.
* **Nighttime Masking:** Leveraged temporal cycles to hard-code predictions to zero during confirmed non-daylight periods, completely eliminating nocturnal sensor noise.

---

## 📁 Repository Structure

```text
├── data/
│   ├── train.csv               # Training dataset
│   └── test.csv                # Evaluation test dataset
├── notebooks/
│   └── mlx_solution_zero.ipynb # Complete Feature Engineering, Training & Inference Pipeline
├── src/
│   ├── features.py             # Feature engineering functions (Cyclic transforms, wind vectors)
│   └── models.py               # Model training loops and cross-validation setup
├── submissions/
│   └── submission.csv          # Final Rank 4 submission file
├── requirements.txt            # Environment dependencies
└── README.md                   # Project documentation
