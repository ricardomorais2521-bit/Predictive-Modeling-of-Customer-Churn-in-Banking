# Predictive Modeling of Customer Churn in Banking

Logistic regression versus a pruned classification tree to predict which bank customers will leave, evaluated on a held-out test set with sensitivity and AUC rather than accuracy.

Group project for *Aprendizagem Automática e Prospeção de Dados* (Machine Learning and Data Mining), Faculdade de Ciências da Universidade de Lisboa, 2025/26.
Authors: Ricardo Morais, Martim Luzio and Miguel Fonseca. The full report is in Portuguese: [`report/Churn_Report_PT.pdf`](report/Churn_Report_PT.pdf).

## Objective

Build and compare two classifiers of different nature to predict customer churn (`Abandono`), and identify which one better supports a retention strategy, where missing a customer who leaves costs more than contacting one who would have stayed.

## Data and split

- Banking customer data with demographic, product and account variables (age, balance, credit score, tenure, geography, gender, number of products, credit card, active membership, estimated salary).
- Rows with missing values were removed. The number-of-products variable had very few customers with 3 and 4 products (22 and 3), so these were pooled into a "3+" category.
- Training set: 1,000 customers (19.0% churn). Test set: 600 customers (21.2% churn). Different random seeds were fixed for each set.
- Because the classes are imbalanced, accuracy was not used as the main metric. Models are compared on sensitivity, specificity and AUC.
- The dataset itself is not included in this repository.

## Methods

| Model | Details |
|---|---|
| Logistic regression | Binomial family with logit link. Bidirectional stepwise selection by AIC. Decision threshold chosen with the Youden index (J = sensitivity + specificity - 1), giving 0.15 instead of the default 0.5. |
| Classification tree | Recursive binary partitioning, pruned by cost-complexity: the complexity parameter (CP) with the lowest cross-validation error was selected. |

## Results (test set, n = 600)

| Model | Accuracy | Sensitivity | Specificity | AUC |
|---|---|---|---|---|
| Logistic regression (Youden threshold 0.15) | 69.3% | **87.4%** | 64.5% | **0.83** |
| Classification tree | 80.8% | 31.5% | 94.1% | 0.73 |

![ROC curves of both classifiers](figures/roc_comparison.png)

Main findings:

- Logistic regression identifies 111 of the 127 customers who left. The tree identifies only 40. The tree has higher accuracy because it favours the majority class, which makes it unsuitable for retention.
- The variables retained by the stepwise search were number of products, age, active membership, geography, gender, estimated salary and credit score. The first five are the most significant.
- In the exploratory analysis, customers with one product left in about 28% of cases, German customers left more often than French and Spanish ones, and inactive customers left more often than active ones.
- The price of the Youden threshold is a lower specificity (64.5%), which means more customers are flagged unnecessarily. This is acceptable when retention actions are cheap compared with losing a customer.

![Classification tree](figures/classification_tree.png)

## Limitations

- Models were built and evaluated on a single train/test partition, so results can vary with the split. Repeated resampling or cross-validation would give a more stable estimate.
- Class imbalance was handled through the threshold only. Resampling methods such as SMOTE, or more flexible models such as random forests, were left as future work.

## Repository contents

```
report/    Final report (Portuguese)
figures/   ROC curves, classification tree and churn rate by category (taken from the report)
code/      R script with the full analysis (churn_analysis.R)
```

## Reproducibility

The analysis was written in R. Place the dataset in a local `data/` folder (not tracked by git) and run `code/churn_analysis.R`.

## Notes

The report states that the AI model Gemini (Google) was used as a study-support tool.
