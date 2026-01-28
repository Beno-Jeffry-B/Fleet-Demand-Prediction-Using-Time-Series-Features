# 🚕 Fleet Demand Prediction Using Time-Series Classification

## 📌 Project Overview

This project analyzes historical ride-hailing trip data from multiple fleet operators to identify **high-demand pickup zones and time windows**.  
The goal is to help fleet operators **proactively position vehicles** to reduce customer wait times and improve operational efficiency.

The dataset represents **real-world production-like data**, containing:
- Multiple CSV files
- Inconsistent schemas across years
- Temporal gaps
- Imbalanced demand patterns

The solution focuses on **time-aware data processing**, **feature engineering**, and a **simple, explainable classification model**.

---

## 🎯 Problem Statement

**Can we predict whether a given zone and hour will experience high pickup demand using historical trip patterns?**

This is framed as a **binary classification problem**:
- `1` → High demand  
- `0` → Normal demand  

---

## 🗂️ Folder Structure

```
root_project_folder/
│
├── data/
│   └── raw/
│       ├── uber_2014/        # Monthly Uber trip files (2014)
│       ├── uber_2015/        # Uber trip data with LocationID (2015)
│       ├── fhv_non_uber/     # Non-Uber FHV trip files
│       ├── aggregated/      # Pre-aggregated daily statistics
│       └── lookup/          # Taxi zone lookup (LocationID → Zone)
│
├── notebooks/
│   ├── 01_exploration.ipynb        # Initial data loading & EDA
│   ├── 02_integration_eda.ipynb    # Aggregation & time-based analysis
│   └── 03_modeling.ipynb           # Feature engineering & modeling
│
└── README.md
```


---

## 📊 Data Understanding & EDA

### Key steps:
- Merged all Uber 2014 monthly files into a single dataset
- Parsed timestamps and extracted:
  - Hour
  - Day
  - Weekday
- Visualized:
  - Hourly pickup distribution
  - Weekday × hour heatmaps
  - Base-level demand patterns

### Key insights:
- Demand rises sharply during evening hours
- Strong weekday vs weekend patterns
- Demand is highly skewed and imbalanced

---

## ⏱️ Time-Series Preparation

To correctly model demand over time:

- Created **hourly demand counts per zone**
- Built a **complete hourly time grid** per location
- Explicitly filled missing hours with `0` pickups  
  (important to distinguish *no demand* from *missing data*)

---

## 🛠️ Feature Engineering

For each zone-hour combination:

- Lag features:
  - Demand in previous hour (`lag_1`)
  - Demand at same hour previous day (`lag_24`)
- Rolling averages:
  - 3-hour rolling mean
  - 24-hour rolling mean
- Time features:
  - Hour of day
  - Weekday

These features capture **short-term momentum** and **daily seasonality**.

---

## 🧠 Target Definition

High demand is defined using a **percentile-based threshold**:

- Top 20% of hourly pickup counts → `high_demand = 1`
- Remaining → `0`

This avoids hard-coded cutoffs and adapts to data distribution.

---

## 🤖 Modeling Approach

- Model: **Logistic Regression**
- Reason:
  - Fast
  - Interpretable
  - Strong baseline for operational use
- Class imbalance handled using:
  - `class_weight = "balanced"`
- Time-aware train-test split:
  - No shuffling to avoid data leakage

---

## 📈 Evaluation & Interpretation

### Metrics:
- Precision, Recall, F1-score
- Confusion matrix

### Key observations:
- Model remains quiet during low-demand periods (low false alarms)
- Strong recall during peak demand windows
- Recent demand (`lag_1`, `lag_24`) is the strongest predictor

### Visual diagnostics:
- Actual vs predicted labels (normal vs peak windows)
- Predicted probability trends over time
- Feature importance via model coefficients

---

## 🔍 Key Takeaways

- Demand patterns are **highly time-dependent**
- Recent history is more predictive than calendar features alone
---

## 🚀 Future Improvements
- Compare with tree-based models for non-linear effects
---

## 📌 Conclusion

This project demonstrates an end-to-end pipeline for **time-series demand classification**, from raw data ingestion to actionable insights.  
The focus is on **clarity, correctness, and business relevance**, making the approach suitable for real-world fleet operations.


