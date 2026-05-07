# Interpretable Cardiac Risk Prediction

A deep learning project that predicts cardiac disease risk from clinical features and explains predictions using SHAP (SHapley Additive exPlanations).

## Overview

| Item | Detail |
|---|---|
| Dataset | [UCI Heart Disease (Cleveland)](https://www.kaggle.com/code/mragpavank/heart-disease-uci) — 302 samples, 13 features |
| Model | Multi-Layer Perceptron (MLP) |
| XAI Method | SHAP — KernelExplainer |
| Task | Binary classification: Cardiac Risk / No Risk |

## Project Structure

```
cardiac-risk-prediction/
├── heart.csv                  # UCI Heart Disease dataset
├── heart-disease-uci.ipynb    # Main notebook (full pipeline)
├── SETUP.md                   # Setup and run instructions
├── README.md
└── outputs/                   # Generated plots (created on run)
    ├── class_distribution.png
    ├── correlation_heatmap.png
    ├── feature_distributions.png
    ├── boxplots.png
    ├── training_history.png
    ├── confusion_matrix.png
    ├── roc_curve.png
    ├── shap_summary_plot.png
    ├── shap_feature_importance.png
    └── shap_local_explanation.png
```

## Setup

Requires Python 3.10+. Full instructions in [SETUP.md](SETUP.md).

```bash
python3 -m venv venv
venv/bin/pip install numpy pandas matplotlib seaborn scikit-learn tensorflow shap jupyter
venv/bin/jupyter notebook heart-disease-uci.ipynb
```

## Notebook Sections

1. **Import Libraries** — dependencies and seed setup
2. **Load Dataset** — load and rename columns
3. **Data Preprocessing** — missing values, duplicates, scaling, train/test split
4. **Exploratory Data Analysis** — class distribution, correlations, feature plots
5. **Deep Learning Model** — MLP architecture, training with early stopping
6. **Model Evaluation** — accuracy, precision, recall, F1, confusion matrix, ROC-AUC
7. **Explainable AI (SHAP)** — KernelExplainer setup and SHAP value computation
8. **SHAP Plots** — global summary plot, feature importance bar, local waterfall plot
9. **Prediction vs Explanation** — per-patient table with top contributing features
10. **Summary** — metrics and output file listing

## Results

| Metric | Score |
|---|---|
| Accuracy | 78.69% |
| Precision | 0.7500 |
| Recall | 0.9091 |
| F1-Score | 0.8219 |
| ROC-AUC | 0.8788 |

## Data Source

Dataset obtained from Kaggle: [Heart Disease UCI by mragpavank](https://www.kaggle.com/code/mragpavank/heart-disease-uci).  
Original source: UCI Machine Learning Repository — Cleveland Heart Disease dataset.

## Features

| Column | Description |
|---|---|
| Age | Age in years |
| Sex | 1 = male, 0 = female |
| ChestPainType | 0–3 (0 = asymptomatic, 3 = typical angina) |
| RestingBP | Resting blood pressure (mm Hg) |
| Cholesterol | Serum cholesterol (mg/dl) |
| FastingBS | Fasting blood sugar > 120 mg/dl (1 = true) |
| RestingECG | Resting ECG result (0–2) |
| MaxHR | Maximum heart rate achieved |
| ExerciseAngina | Exercise-induced angina (1 = yes) |
| Oldpeak | ST depression induced by exercise |
| ST_Slope | Slope of peak exercise ST segment (0–2) |
| MajorVessels | Number of major vessels colored by fluoroscopy (0–4) |
| Thalassemia | Thalassemia type (0–3) |
| **Target** | **1 = Cardiac Risk, 0 = No Risk** |
