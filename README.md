# Vahan Lead Source Cohort Performance
## Overview
This project analyzes lead-source cohort performance to identify which lead sources are most effective at converting leads into FT candidates.
The analysis was conducted as part of a Vahan data analytics case study.

## Business Questions
1. Which three lead-source cohorts perform the best, and which metric should be used to evaluate them?
2. How can the raw lead-level data be aggregated to provide meaningful cohort-level insights using SQL?
3. Which factors are associated with the likelihood of FT conversion, and how well can a machine learning classification model identify FT candidates?

## Approach

The workflow for this Analysis is as follows:
1. Data Understanding
2. Data Cleaning
3. Lead-Source Cohort Analysis
4. SQL Aggregation
5. Machine Learning
6. Model Evaluation
7. Business Recommendations

## Key Metrics
**FT Conversion rate** is used as the key metrics for evaluation the performance and FT volume is used as the supporting  measure of scale

## Dataset Overview
The dataset contains 18,198 lead records with FT conversion as the primary outcome.
| Metric             |       Value |
| ------------------ | ----------: |
| Total Leads        |      18,198 |
| FT Leads           |          17 |
| Non-FT Leads       |      18,181 |
| FT Conversion Rate |      0.093% |
| Cohort Dimension   | Lead Source |

The extremely low FT rate creates a significant class imbalance, which is considered when evaluating the machine-learning model.

## 1. Data Understanding

The initial analysis focused on understanding the structure of the lead data, available variables, lead-source distribution, and FT conversion outcome.

The key analytical dimensions were:

- Lead Source used to define cohorts

- FT Conversion  used as the primary outcome

- Lead Volume used to understand cohort scale


## 2. Data Cleaning

The dataset was checked for:

- Duplicate records
  
- Missing values

- Data types
  
- Date fields
  
- Target consistency

- Logical inconsistencies

After the required cleaning and validation checks, the resulting dataset was used for cohort analysis, SQL aggregation, and machine learning.

## 3. Lead-Source Cohort Analysis

##### Objective

- Identify the three lead-source cohorts with the strongest FT conversion performance.
  
##### Cohort Definition

- lead_source was selected as the cohort dimension because the business objective is to compare the effectiveness of different lead-sourcing channels.

##### Evaluation

- FT Conversion Rate was used as the primary ranking metric, while FT count and lead volume were considered as supporting measures.

#### Top 3 Cohorts

  The top three cohorts were identified based primarily on FT conversion rate, with lead volume and FT count considered when interpreting the results.

## 4. SQL Aggregation

##### Objective

- Provide an aggregated view of lead-source performance that can be used for business monitoring.

- The raw lead-level data was aggregated at the lead-source level, consistent with the cohort analysis.

The aggregation includes:

- Total leads
  
- FT conversions

- FT conversion rate

**SQL**
```sql
SELECT
    lead_source,
    COUNT(*) AS total_leads,
    SUM(ft_after_first_attempt) AS ft_candidates,
    ROUND(
        100.0 * SUM(ft_after_first_attempt) / COUNT(*),
        3
    ) AS ft_conversion_rate
FROM raw_leads
GROUP BY lead_source
ORDER BY ft_conversion_rate DESC;
```
**Output**
| Lead Source                         | Total Leads | FT Candidates | FT Conversion Rate |
| ----------------------------------- | ----------: | ------------: | -----------------: |
| Khanna- 2W 26th Jul                 |       1,546 |             6 |             0.388% |
| PreOb-Ob Fees Paid 29th Jul (set 2) |       1,558 |             5 |             0.321% |
| PreOb-Ob Fees Paid 29th Jul (set 1) |       1,483 |             2 |             0.135% |
| Single Referral > 7 days- 24th Jul  |       1,500 |             2 |             0.133% |
| OLX - Ashwin - 2W - 17 Jul          |       5,182 |             2 |             0.039% |

The aggregation shows substantial differences in FT conversion efficiency across lead sources. The highest conversion rate is observed for Khanna- 2W 26th Jul, followed by PreOb-Ob Fees Paid 29th Jul (set 2) and PreOb-Ob Fees Paid 29th Jul (set 1).

Lead-source-level aggregation provides a direct comparison of sourcing-channel volume and FT conversion efficiency and therefore aligns with the primary business question.

## 5. Machine Learning

**Objective**

Identify factors associated with the likelihood of FT conversion and evaluate whether a classification model can identify FT candidates.

**Model**

Logistic Regression was selected because FT conversion is a binary outcome and the model provides interpretable coefficients that can be used to understand the direction of association between features and FT.

**Preprocessing**

The modelling pipeline includes:

- Missing-value treatment
  
- Numerical feature scaling
  
- Categorical feature encoding
  
- Class balancing where appropriate

The preprocessing steps are included within the modelling pipeline to prevent information from the test set influencing model training.

### 6. Model Evaluation

Because FT represents only 17 of 18,198 observations, accuracy alone is not an appropriate measure of model performance.

The model was therefore evaluated using:

- Precision
  
- Recall
  
- F1 Score
  
- ROC-AUC
  
- Confusion Matrix
  
 **Results**

| Metric    |   Score |
| --------- | ------: |
| Accuracy  |  99.84% |
| Precision |  33.33% |
| Recall    | 100.00% |
| F1 Score  |  50.00% |
| ROC-AUC   |  99.95% |

The model identified all observed FT cases in the test set, resulting in 100% recall. However, six non-FT leads were classified as FT, resulting in a precision of 33.3%.

## 7. Business Recommendations

**Lead Source Optimization**

- Monitor FT conversion rate by lead source to identify sourcing channels that consistently demonstrate stronger conversion efficiency.

**Lead Prioritization**

- A predictive model could potentially support lead prioritization by identifying leads with higher estimated FT likelihood.

**Data Quality for Future Modelling**

- Future predictive models should use only information available before the calling process begins. Variables generated during calling or conversion should be    excluded to avoid information leakage.

Increase FT Observations

- The dataset contains only 17 FT-positive observations. Collecting more FT outcomes would improve the reliability of model evaluation and feature-level conclusions.

**Limitations**

1. The dataset contains only 17 FT-positive observations, resulting in severe class imbalance.
2. Model performance metrics should therefore be interpreted cautiously.
3. Candidate identifiers should not be treated as meaningful predictive features.
4. Further validation on future leads with more FT outcomes would be required before production use.
