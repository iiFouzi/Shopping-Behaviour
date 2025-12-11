# Shopping Behavior Analysis - Code Insights

## Project Overview
This analysis examines shopping behavior patterns through data cleaning, exploratory data analysis (EDA), and feature engineering on a shopping dataset.

---

## Data Processing Pipeline

### 1. **Data Loading & Initial Exploration**
- **Source:** `shop_patterns.csv`
- **Libraries Used:** pandas, matplotlib, numpy, seaborn
- **Key Variables Defined:**
  - Numerical columns: `Age`, `Purchase Amount (USD)`, `Review Rating`
  - Categorical columns: `Gender`, `Category`, `Frequency of Purchases`

### 2. **Missing Data Handling**
The code implements a strategic approach to handling missing values:

#### Numerical Columns Strategy:
- **Method:** Mode imputation (most frequent value)
- **Rationale:** Minimal data distortion since mode is already the most common value
- **Columns affected:** Age, Purchase Amount, Review Rating
- **Result:** All missing values replaced with mode and converted to integers

#### Categorical Columns Strategy:
- **Approach:** Meaningful placeholder values
- **Mappings:**
  - Gender → `Unknown_Gender`
  - Category → `Other`
  - Frequency of Purchases → `Unknown_Frequency`

#### Decision Analysis:
The code evaluated imputation methods through visualization:
- Original data (with nulls)
- Mean imputation
- Median imputation
- Mode imputation

Mode was selected as it preserves the original distribution best.

---

## Exploratory Data Analysis (EDA)

### Categorical Analysis
**Visualization:** Pie charts for 8 categorical features
- **Categories analyzed:**
  - Gender
  - Product Category
  - Season
  - Shipping Type
  - Promo Code Usage
  - Payment Method
  - Frequency of Purchases
  - Previous Purchases

**Key insights:** Distribution patterns for customer demographics and purchasing habits

### Numerical Analysis
**Visualizations:** Histograms with KDE curves and box plots

**Metrics tracked:**
- **Age distribution** - shows customer age spread
- **Purchase Amount (USD)** - spending patterns with mean and median markers
- **Previous Purchases** - customer history/loyalty indicator

**Statistical measures:** Mean, median calculated for each numerical feature to identify central tendencies

### Correlation Analysis
**Heatmap visualization** showing relationships between numerical variables:
- Identifies which variables are positively/negatively correlated
- Helps understand feature relationships in the dataset

---

## Feature Engineering

### New Features Created

#### 1. **Loyalty Indicator: `Loyal (gdnp)`**
- **Purpose:** Segment customers by purchase frequency
- **Grouping:**
  - **Loyal customers:** Weekly, Fortnightly, Bi-weekly, Unknown_Frequency → "Yes"
  - **Non-loyal customers:** Annually, Quarterly, Every 3 Months, Monthly → "No"
- **Business value:** Identifies target customers for loyalty programs or discounts

#### 2. **Spending Level: `High_Spender`**
- **Threshold:** Above mean purchase amount
- **Categories:** "High spender" vs "Low spender"
- **Use case:** Segment marketing campaigns by customer spending power

#### 3. **Review Sentiment: `Positive_Review`**
- **Threshold:** Rating ≥ 4
- **Categories:** "Positive Review" vs "Negative Review"
- **Use case:** Identify satisfaction levels and correlate with other variables

---

## Data Cleaning Output

### Final Dataset Characteristics:
- **Status:** All missing values handled
- **Features:** Original columns + 3 new engineered features
- **Output:** Saved as `shop_patterns_cleaned.csv`

---

## Analysis Flow Summary

```
Raw Data (shop_patterns.csv)
    ↓
Data Exploration (shape, info, describe, nulls)
    ↓
Missing Value Analysis & Imputation
    ↓
Exploratory Data Analysis (visualizations)
    ↓
Statistical Exploration (correlation)
    ↓
Feature Engineering (loyalty, spending, sentiment)
    ↓
Cleaned Dataset (shop_patterns_cleaned.csv)
```

---

## Key Takeaways

1. **Data Quality:** Successfully identified and handled ~22-50% missing values in certain columns through thoughtful imputation
2. **Customer Segmentation:** Created three actionable features for customer segmentation (loyalty, spending, satisfaction)
3. **Distribution Insights:** Analyzed distributions across 11+ variables to understand customer demographics and behavior
4. **Business Ready:** Final cleaned dataset ready for modeling, dashboarding, or deeper analysis

---

## Potential Next Steps

- Build predictive models using engineered features
- Customer clustering based on behavior patterns
- A/B testing for loyalty programs
- Recommendation system development
- Time-series analysis if temporal data is available
