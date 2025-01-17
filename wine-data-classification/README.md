# Wine Quality Prediction using Decision Trees

## Overview
This project analyzes a wine dataset to classify wine quality using decision trees. The analysis includes splitting the dataset into training and testing sets, building and evaluating decision trees using both "information" and "gini" splitting criteria, pruning the trees for improved generalization, and deriving insights from the most important variables. Enhanced visualizations are provided to make decision-making processes clear to stakeholders.

---

## Dataset
The dataset contains physicochemical attributes and quality ratings of wine samples. Key features include:
- **fixed acidity**: Level of fixed acids.
- **volatile acidity**: Level of volatile acids.
- **citric acid**: Citric acid concentration.
- **residual sugar**: Sugar content remaining after fermentation.
- **chlorides**: Salt content.
- **free sulfur dioxide**: Free form of SO2.
- **total sulfur dioxide**: Bound and free forms of SO2.
- **density**: Density of the wine.
- **pH**: Acidity level.
- **sulphates**: Sulfate content.
- **alcohol**: Alcohol content as a percentage.
- **quality**: Target variable, representing wine quality.

Data Source: [Wine Quality Dataset](https://archive.ics.uci.edu/ml/datasets/Wine+Quality)

---

## Concepts of Machine Learning Covered
This project demonstrates fundamental concepts of machine learning:
1. **Supervised Learning**:
   - Decision Trees: A supervised learning algorithm for classification tasks.
   - Evaluation of models using accuracy as a metric.
2. **Data Splitting**:
   - Splitting the dataset into training (70%) and testing (30%) subsets to evaluate model generalization.
3. **Model Training and Testing**:
   - Building decision trees using different splitting criteria (“Information” and “Gini”).
   - Pruning trees to balance bias and variance.
4. **Model Evaluation**:
   - Comparing training and testing accuracy for both unpruned and pruned models.
   - Interpreting feature importance and deriving decision rules.
5. **Visualization**:
   - Enhanced visualizations of decision trees with rules and probabilities.

---

## Steps in the Analysis

1. **Data Loading and Preparation**:
   - The dataset is loaded from a URL and converted into a suitable format for classification (e.g., converting the target variable to a factor).

2. **Data Splitting**:
   - A 70-30 split is performed to create training and testing datasets for model evaluation.

3. **Decision Tree Construction**:
   - Unpruned trees are built using both “Information” and “Gini” splitting criteria.

4. **Model Evaluation**:
   - Accuracy is calculated for both training and testing datasets to assess overfitting and generalization.

5. **Pruning**:
   - Trees are pruned using the complexity parameter (`cp`) that minimizes cross-validation error (`xerror`).

6. **Visualization**:
   - Trees are visualized with decision rules and probabilities to enhance interpretability.

7. **Insights**:
   - Key insights are derived, including the most important feature (alcohol) influencing wine quality.

---

## Prerequisites
- **R (version >= 4.0)**
- Libraries: `rpart`, `rpart.plot`, `knitr`, `kableExtra`

Install required libraries:
```R
install.packages(c("rpart", "rpart.plot", "knitr", "kableExtra"))
```

---

## Instructions to Run the Analysis
1. Clone the repository:
   ```bash
   git clone <https://github.com/stefanyayala/Machine-Learning-Fundamentals>
   cd wine_quality_analysis
   ```
2. Open `wine_quality_analysis.Rmd` in RStudio or any R Markdown-compatible editor.
3. Knit the file to generate the analysis in your preferred format (HTML, PDF, or Word).
4. View the decision tree visualizations in the `visualizations/` directory.

---

## Key Results
| Model               | Testing Accuracy |
|---------------------|------------------|
| Unpruned (Info)     | 82.11%          |
| Pruned (Info)       | 83.01%          |
| Unpruned (Gini)     | 80.31%          |
| Pruned (Gini)       | 80.92%          |

- The pruned decision tree using the **Information Criterion** performed the best in terms of testing accuracy (83.01%).
- **Alcohol content** emerged as the most influential feature in predicting wine quality.

---

## Example Visualizations
Below is an example of an enhanced visualization:

![Pruned Decision Tree (Gini)](wine-data-classification/prune-decision-tree-gini.png)

---

## Future Improvements
1. Explore ensemble methods like Random Forests and Gradient Boosting for better performance.
2. Add precision, recall, and F1-score for a more comprehensive evaluation.
3. Apply hyperparameter tuning to optimize decision tree parameters.
4. Experiment with additional datasets for generalization.
