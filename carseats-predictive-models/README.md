# Carseats Data Analysis Project

## Overview

This project involves analyzing a dataset of car seat sales, referred to as `carseats_data.rdata`, to build predictive models that classify whether unit sales at a given store are high or low. The primary objective is to predict the `Target` variable (which indicates whether sales are high or low based on a threshold) using various store-level features.

The analysis includes model building with multiple algorithms, such as Random Forest, Naive Bayes, Decision Trees, and K-Nearest Neighbors (KNN). The performance of these models is evaluated using metrics such as accuracy, confusion matrix, and gain charts to identify the most suitable model for classifying high vs. low sales.

## Dataset Description

The dataset (`carseats_data.rdata`) contains information about car seat sales at different stores. Below is a description of the key variables:

| Variable Name | Description |
|----------------|-------------|
| **Target** | Whether unit sales are high (≥ 8000) or low (< 8000). |
| **CompPrice** | Price charged by competitors at each location. |
| **Income** | Community-level income (in thousands of dollars). |
| **Advertising** | Local advertising budget for the company at each location (in thousands of dollars). |
| **Population** | Regional population (in thousands). |
| **Price** | Price for car seats at each site. |
| **ShelveLoc** | Quality of the shelving location: Bad, Good, or Medium. |
| **Age** | Average age of the local population. |
| **Education** | Education level at each location. |
| **Urban** | Indicates whether the store is in an urban location ("Yes" for urban, "No" for rural). |
| **US** | Indicates whether the store is in the US ("Yes" for US, "No" for outside the US). |

## Project Objective

The goal of this analysis is to build a model that predicts whether sales are high or low based on the features provided in the dataset. Specifically, the task is to classify the `Target` variable as either "Yes" (high sales) or "No" (low sales).

### Steps Involved:
1. **Data Preprocessing**: Load and inspect the dataset, handle missing data, and normalize features where necessary.
2. **Model Building**: Train several machine learning models, including:
   - Random Forest
   - Naive Bayes
   - Decision Trees
   - K-Nearest Neighbors (KNN)
3. **Model Evaluation**: Evaluate models using performance metrics such as accuracy, confusion matrix, and gain charts.
4. **Model Comparison**: Compare the performance of models to identify the best-performing one based on accuracy and other relevant metrics.
5. **Model Interpretation**: Draw conclusions about the suitability of each model and its potential for future predictions.

## Methodology

### Data Preparation

1. **Loading the Data**: The dataset is loaded from an `.Rdata` file containing the `carseats_data` object.
2. **Splitting the Data**: The data is split into training and testing sets (80% training, 20% testing) to evaluate model performance.
3. **Feature Normalization**: Features are normalized (where necessary) to ensure consistency across models, especially for distance-based models like KNN.

### Model Building

1. **Random Forest**:
   - Random Forest is used to classify the `Target` variable based on the store-level features.
   - This model is trained using 300 trees and evaluated using a confusion matrix and gain charts.
   
2. **Naive Bayes**:
   - Naive Bayes is used to predict the likelihood of high vs. low sales based on the input features.
   - The model's performance is compared to the Random Forest model using gain charts.

3. **Decision Trees**:
   - A decision tree is built to classify sales levels.
   - This model is also evaluated using a confusion matrix and compared to other models.

4. **K-Nearest Neighbors (KNN)**:
   - The KNN algorithm is used with different values of `k` (1 and 3) to classify the `Target` variable.
   - KNN is trained using normalized data and evaluated for accuracy.

### Model Evaluation

- **Accuracy**: All models are evaluated for accuracy by comparing predicted vs. actual sales levels in the test set.
- **Confusion Matrix**: The confusion matrix is used to measure the performance of each model, focusing on false positives and false negatives.
- **Gain Charts**: Gain charts are plotted to visually compare the effectiveness of the models in classifying high vs. low sales.

## Dependencies

To run the analysis, the following R packages are required:

- `randomForest`
- `klaR`
- `caret`
- `ROCR`
- `e1071`
- `rpart`
- `MASS`
- `FNN`

You can install these packages by running the following commands in your R console:

```r
install.packages(c("randomForest", "klaR", "caret", "ROCR", "e1071", "rpart", "MASS", "FNN"))
```

## Usage Instructions

1. Load the dataset:
   - The dataset is automatically loaded from the `carseats_data.rdata` file.
   
2. Run the analysis:
   - Open the `carseats-prediction.Rmd` file and knit it to generate the output report (HTML file).
   
3. Interpret the results:
   - The output HTML file will contain all the analysis, model evaluations, and visualizations.
   - Key insights, including the best-performing model and recommendations, will be highlighted.

## Conclusion

This project provides a comprehensive analysis of car seat sales, using various machine learning models to predict whether sales are high or low. Based on model evaluation, the most suitable model is selected for future predictions, and a detailed performance comparison is provided. This analysis is valuable for decision-making regarding pricing, advertising, and store placement strategies.
