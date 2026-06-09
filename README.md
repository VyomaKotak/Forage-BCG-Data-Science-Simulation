# BCG X Data Science Job Simulation — PowerCo Customer Churn Analysis

> **Completed BCG's Data Science Job Simulation on Forage** — analyzed SME customer churn data for a major European utility using Python. Built and optimized a Random Forest classifier, engineered 62 predictive features, visualized trends across 14,606 customers, and produced client-facing deliverables at each stage. Gained hands-on experience in EDA, hypothesis-driven feature engineering, machine learning, and executive communication.

---

## Simulation Details

| | |
|--|--|
| **Platform** | [Forage](https://www.theforage.com) |
| **Programme** | BCG Data Science Job Simulation |
| **Certificate Issued by** | Forage in collaboration with BCG Consulting |
| **Role simulated** | Junior Data Scientist, BCG X |
| **Supervising (simulated)** | Estelle Altazin — Senior Data Scientist |
| **Client** | PowerCo (major European gas & electricity utility) |

---

## Background

PowerCo supplies gas and electricity to corporate, SME, and residential customers across Europe. Following energy market liberalisation, the SME segment is experiencing significant customer churn. PowerCo engaged BCG X to diagnose the drivers of churn and build a predictive model to support a **20% targeted discount intervention** — applied on the 1st working day of each month to the customers most at risk.

**Central hypothesis tested:** *"Price sensitivity is the primary driver of SME customer churn."*

---
## Objective

This repository showcases my ability to apply data science techniques to real-world business problems — from framing a hypothesis and communicating with stakeholders, through to building and evaluating a production-oriented churn model. I am eager to bring these skills to a data science team where I can contribute to solving complex, client-facing challenges.

## Repository Structure

```
BCG_DataScience_Forage/
│
├── BCG_X_Email.docx                        # Task 1 — Initial client communication to AD
├── PowerCo_EDA_Vyoma.ipynb                 # Task 2 — Exploratory Data Analysis
├── PowerCo_FeatureEngineering_Vyoma.ipynb  # Task 3 — Feature Engineering
├── PowerCo_Modelling_Vyoma.ipynb           # Task 4 — Random Forest Modelling
│
├── data/
│   ├── client_data.csv                     # Raw customer profiles (14,606 customers)
│   ├── price_data.csv                      # Monthly pricing history (193,002 records)
│   ├── clean_data_after_eda.csv            # Output of EDA notebook
│   └── data_for_predictions.csv           # Final 62-feature model-ready matrix
│
└── README.md
```

---

## Simulation Highlights

### Task 1 — Business Understanding & Client Communication
**Deliverable:** `BCG_X_Email.docx`

Formulated the churn hypothesis as a data science problem and communicated a structured analytical plan to the Associate Director. The email covers:
- **5 data categories** to request from PowerCo: customer profiles, historical pricing & billing, energy consumption patterns, customer service records, and competitor/market benchmarks
- **5-step methodology**: Business Understanding → EDA & Cleaning → Feature Engineering → Modelling & Evaluation → Insights & Recommendations

*Skills practised: business communication, hypothesis framing, analytical planning*

---

### Task 2 — Exploratory Data Analysis
**Deliverable:** `PowerCo_EDA_Vyoma.ipynb`

Full profiling of two datasets totalling 207,608 rows and verifying the price sensitivity hypothesis through statistical analysis.

| Dataset | Size | Key Content |
|---------|------|-------------|
| `client_data.csv` | 14,606 × 26 | Consumption, margins, tenure, channel, churn flag |
| `price_data.csv` | 193,002 × 8 | Monthly off-peak, peak, mid-peak tariffs per customer |

**Key findings:**
- **9.7% churn rate** across the SME base — a severe class imbalance (~9.3:1) requiring specialised handling in modelling
- Customers with **≤4 months tenure** churn at significantly higher rates than the average
- Most customers are on **off-peak-only tariffs** — peak and mid-peak prices are near zero for the majority, making off-peak price movements the most analytically important variable
- ~1,383 customers in the client dataset have no price history, flagged for handling at merge time
- No duplicate customer IDs; zero data integrity issues

*Skills practised: EDA, data cleaning, statistical analysis, data visualisation (matplotlib, seaborn)*

---

### Task 3 — Feature Engineering
**Deliverable:** `PowerCo_FeatureEngineering_Vyoma.ipynb`

Transformed raw data into a 62-feature model-ready matrix using a structured four-stage framework:

| Stage | Action | Example |
|-------|--------|---------|
| **A — Remove** | Drop identifiers and raw dates | `id`, `date_activ` |
| **B — Expand** | Derive numeric time features from dates | `months_to_end`, `tenure` |
| **C — Combine** | Build domain-informed composite features | `has_gas` binary encoding |
| **D — Merge** | Join client + price features on `id` | 14 new price-derived columns |

**Key engineered features:**

- **Time features** (reference date: 2016-01-01): `months_activ`, `months_to_end`, `months_modif_prod`, `months_renewal`, `tenure`
- **Estelle's hypothesis feature**: `offpeak_diff_dec_january_energy` and `offpeak_diff_dec_january_power` — the difference between December and January off-peak prices, directly testing whether year-start price jumps predict churn
- **6 mean period difference features**: off-peak vs peak vs mid-peak price spreads (variable and fixed)
- **6 maximum monthly difference features**: worst-month price spike per customer across the year

**Output:** `data_for_predictions.csv` — 14,606 rows × 62 features

*Skills practised: feature engineering, domain-driven feature design, dataset merging, pandas*

---

### Task 4 — Predictive Modelling
**Deliverable:** `PowerCo_Modelling_Vyoma.ipynb`

Trained and evaluated a Random Forest classifier on the engineered feature matrix.

**Model configuration:**

| Parameter | Value | Rationale |
|-----------|-------|-----------|
| `n_estimators` | 1,000 | Stable, converged ensemble |
| `class_weight` | `'balanced'` | Automatically up-weights the 9.7% minority (churners) |
| `random_state` | 42 | Full reproducibility |
| Train/test split | 75% / 25% stratified | Preserves churn rate in both sets |

**Evaluation approach** — accuracy alone is misleading (a "predict never churn" model scores 90.3%), so evaluation focused on:

| Metric | Business meaning |
|--------|-----------------|
| **Recall** | Fraction of real churners we catch — the business-critical metric; a missed churner is lost revenue |
| **Precision** | Of customers we flag, how many actually churn — controls wasted retention spend |
| **F1 Score** | Harmonic mean of precision and recall; primary optimisation target |
| **ROC-AUC** | Threshold-agnostic separability measure |
| **PR Curve** | More informative than ROC for heavily imbalanced datasets |

**Feature importance findings:**

1. **Price features dominate** — off-peak price changes (Dec–Jan delta, year-on-year, and 6-month movements) are the strongest churn predictors, providing **direct empirical validation of the price sensitivity hypothesis**
2. **`net_margin` and `cons_12m`** also rank highly — the scale and profitability of a customer's relationship matters
3. **Time features** (`months_activ`, `tenure`, `months_to_end`) confirm newer and contract-expiring customers are at greater risk

**Business recommendation:** Price is the most actionable lever. Customers facing the largest off-peak price increases — particularly those with a sharp December-to-January jump — should be prioritised for the 20% retention discount before the renewal decision is made.

*Skills practised: Random Forest, class imbalance handling, model evaluation, feature importance analysis, scikit-learn*

---

## Skills Demonstrated

| Category | Skills |
|----------|--------|
| **Business Understanding** | Hypothesis framing, translating business problems into data science tasks, executive communication |
| **Data Analysis** | EDA, data cleaning, missing value handling, statistical profiling |
| **Feature Engineering** | Time-based features, price-delta features, dataset merging, domain-informed design |
| **Machine Learning** | Random Forest, class imbalance (balanced weights, SMOTE awareness), train/test splitting |
| **Model Evaluation** | Precision, Recall, F1, ROC-AUC, Precision-Recall curves, confusion matrix interpretation |
| **Data Visualisation** | Distribution plots, churn analysis charts, feature importance plots, ROC/PR curves |
| **Programming** | Python — `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn` |

---

## Skills Developed

- Exploratory Data Analysis (EDA)
- Hypothesis-Driven Feature Engineering
- Machine Learning (Random Forest Classifier)
- Class Imbalance Handling
- Model Interpretation & Evaluation
- Data Visualisation & Storytelling
- Business Communication & Executive Reporting
- Synthesis & Decision Making

---

## Tech Stack

```
Python 3
├── pandas          — data manipulation and merging
├── numpy           — numerical operations
├── matplotlib      — base visualisations
├── seaborn         — statistical plots
└── scikit-learn    — modelling, evaluation, preprocessing
```

---


## Results Summary

| | |
|--|--|
| **Dataset size** | 14,606 customers, 193,002 price records |
| **Features engineered** | 62 (up from 26 raw) |
| **Churn rate** | 9.7% — severe class imbalance |
| **Model** | Random Forest, 1,000 trees, balanced class weights |
| **Primary churn driver** | Off-peak price sensitivity (Dec–Jan delta, YoY change) ✅ hypothesis validated |
| **Secondary drivers** | Net margin, annual consumption, customer tenure |
| **Recommended action** | Target 20% discount at customers with largest off-peak price increases |

---



