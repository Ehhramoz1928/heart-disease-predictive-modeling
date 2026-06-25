# 🫀 heart-disease-predictive-modeling

> **Project Assessment:** An automated cardiovascular screening framework built in R to analyze historical hospital records. This project implements multiple logistic regression models to isolate linear odds ratios, alongside optimized classification and regression random forests to natively capture complex, non-linear risk indicators. Developed for an interdepartmental research team, these models evaluate key physiological metrics (such as `thalach` and `exang`) to bridge the gap between traditional clinical inference and predictive machine learning, shifting diagnostics from reactive treatment to early prevention.

---

## 📋 Table of Contents
* [Dataset Strategy](#-dataset-strategy)
* [Statistical Modeling Architecture](#-statistical-modeling-architecture)
  * [Logistic Regression Models](#1-logistic-regression-models)
  * [Random Forest Frameworks](#2-random-forest-frameworks)
* [Key Metrics Summary](#-key-metrics-summary)
* [Clinical Implementation Conclusions](#-clinical-implementation-conclusions)
* [How to Reproduce the Analysis](#-how-to-reproduce-the-analysis)
* [References & Data Citations](#-references--data-citations)

---

## 📊 Dataset Strategy
[cite_start]The historical clinical dataset analyzed contains detailed diagnostic records compiled across $303$ unique patient observations[cite: 13, 204]. [cite_start]The primary dependent metric is `target` (a binary variable mapping the presence of heart disease as $1$ and absence as $0$)[cite: 14]. 

Key underlying health indicators evaluated include:
* [cite_start]`age` (Continuous, measured in years) [cite: 16]
* [cite_start]`sex` (Categorical biological mapping) [cite: 16]
* [cite_start]`cp` (Chest pain classification scaled from level $0$ to $3$) [cite: 17]
* [cite_start]`trestbps` (Resting blood pressure measured in mmHg upon hospital admission) [cite: 17]
* [cite_start]`chol` (Serum cholesterol recorded in mg/dl) [cite: 17]
* [cite_start]`restecg` (Resting electrocardiographic evaluations) [cite: 17]
* [cite_start]`thalach` (Maximum heart rate achieved during clinical stress tests) [cite: 17]
* [cite_start]`exang` (Exercise-induced angina status: $1$ = yes, $0$ = no) [cite: 17]
* [cite_start]`ca` (Total number of major vessels highlighted via fluoroscopy) [cite: 17, 18]

---

## 🔬 Statistical Modeling Architecture

### 1. Logistic Regression Models
[cite_start]Two distinct parametric configurations were compiled using the logit link function to linearize estimated parameters against changes in patient log-odds[cite: 11, 21].

* [cite_start]**Model #1 (Baseline):** Focuses on direct linear dependencies[cite: 11].
  $$\ln\left(\frac{p}{1-p}\right) = -1.0211 - 0.0175(\text{age}) - 0.0149(\text{trestbps}) - 1.6250(\text{exang}) + 0.0311(\text{thalach})$$
  * [cite_start]*Clinical Insight:* Exponentiating the continuous `thalach` slope ($e^{0.0311} \approx 1.0316$) indicates that every single beat-per-minute increase achieved during testing spikes the physical odds of presenting with heart disease by approximately $3.16\%$[cite: 30, 31].

* [cite_start]**Model #2 (Advanced Interactions):** Introduces a non-linear quadratic parameter for age ($\text{age}^2$) alongside an interaction effect between age and heart rate ($\text{age} \times \text{thalach}$)[cite: 115, 279].
  $$\ln\left(\frac{p}{1-p}\right) = -14.7860 + 0.1609(\text{age}) + 0.0011(\text{age}^2) - 0.0143(\text{trestbps}) - 1.5625(\text{exang}) + 0.1422(\text{thalach}) - 0.0020(\text{age} \times \text{thalach})$$

### 2. Random Forest Frameworks
[cite_start]To naturally map multi-dimensional patient profiles without relying on manually programmed interaction terms, non-parametric ensemble models were deployed under a controlled random seed (`6522048`)[cite: 11, 237, 252, 292].

* [cite_start]**Classification Forest (`target` prediction):** Built using an $85\%$ training configuration ($257$ records) and evaluated against a validation partition ($46$ records). [cite_start]The structural misclassification curve reached its optimal baseline configuration at exactly **$9$ trees**[cite: 209, 220].
* [cite_start]**Regression Forest (`thalach` prediction):** Segmented across an $80/20$ split ($242$ training rows, $61$ testing rows) to forecast peak stress heart rates[cite: 248, 249, 250]. [cite_start]The network successfully minimized out-of-sample Mean Squared Error (MSE) using an architecture of **$46$ trees**[cite: 255, 256].

---

## 🏆 Key Metrics Summary

### Classification Performance Matrix

| Evaluation Metric | Logistic Model #1 | Logistic Model #2 | Random Forest (Training Set) | Random Forest (Testing Set) |
| :--- | :---: | :---: | :---: | :---: |
| **Hosmer-Lemeshow Test ($p$-value)** | [cite_start]$0.3264$ [cite: 37] | [cite_start]**$0.3991$** [cite: 131] | *N/A (Non-parametric)* | *N/A (Non-parametric)* |
| **Global Accuracy** | [cite_start]**$73.60\%$** [cite: 50] | [cite_start]**$73.60\%$** [cite: 148] | [cite_start]**$97.28\%$** [cite: 222] | [cite_start]$69.57\%$ [cite: 228] |
| **Precision (Positive Predictive)** | [cite_start]$73.22\%$ [cite: 51] | [cite_start]$72.97\%$ [cite: 149] | [cite_start]$97.79\%$ [cite: 223] | [cite_start]**$76.92\%$** [cite: 229] |
| **Recall (Clinical Sensitivity)** | [cite_start]$81.21\%$ [cite: 52] | [cite_start]**$81.82\%$** [cite: 151] | [cite_start]$97.08\%$ [cite: 224] | [cite_start]$71.43\%$ [cite: 230] |
| **Area Under the Curve (AUC)** | [cite_start]**$0.8143$** [cite: 63] | [cite_start]$0.8112$ [cite: 170] | *N/A* | *N/A* |

### Continuous Regression Performance (`thalach` Prediction)
* [cite_start]**In-Sample Training RMSE:** $11.5322 \text{ bpm}$ (Average internal prediction deviation) [cite: 263, 270]
* [cite_start]**Out-of-Sample Testing RMSE:** $20.2274 \text{ bpm}$ (True generalization capability on unseen records) [cite: 264, 274]
* [cite_start]*Analytical Insight:* The performance gap across evaluation partitions across both random forest frameworks highlights a classic case of **overfitting** to localized training noise[cite: 231, 232, 265].

---

## 🏛️ Clinical Implementation Conclusions
1. [cite_start]**Model Recommendation:** For automated screening channels, the **Random Forest Classifier** is recommended due to its superior localized pattern matching and high positive precision ($76.92\%$)[cite: 229, 284]. [cite_start]However, for pure clinical risk factor explanation, **Logistic Regression Model #2** provides superior structural transparency[cite: 279, 288].
2. [cite_start]**Balancing Triage Failures:** In hospital diagnostics, missing a high-risk cardiac patient (a false negative) carries severe, life-threatening outcomes[cite: 290]. [cite_start]Tracking precise recall boundaries ensures clinical teams successfully optimize screening workflows, catch underlying risks early, and maximize long-term patient survival outcomes[cite: 291, 293].

---

## 🚀 How to Reproduce the Analysis

### Prerequisites
Ensure you have R or RStudio installed. You will need to install the following missing libraries from your console:

```R
# Install required modeling packages
install.packages(c("tidyverse", "caret", "randomForest", "ResourceSelection", "pROC"))
