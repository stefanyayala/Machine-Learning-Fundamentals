# Predicting Customer Credit Risk

## Project Overview
This project demonstrates a predictive analysis pipeline aimed at assessing customer credit risk using the German Credit dataset. The analysis focuses on binary classification to identify creditworthiness (Good vs. Bad) based on various socio-economic and credit-related factors.

The project is implemented using R Markdown and incorporates data preprocessing, exploratory data analysis (EDA), and the development of machine learning models, including Decision Trees and Random Forests.

## Objective
- Predict creditworthiness of customers (Good vs. Bad) based on their credit and personal attributes.
- Compare the performance of Decision Tree and Random Forest models.
- Identify the most predictive features contributing to credit risk.

## Dataset
- **Source**: German Credit dataset
- **Size**: 1,000 rows and 32 columns.
- **Features**: Includes numerical and categorical variables such as:
  - Credit amount (`AMOUNT`)
  - Duration (`DURATION`)
  - Checking account status (`CHK_ACCT`)
  - Employment status (`EMPLOYMENT`)
  - Response variable (`RESPONSE`): Indicates creditworthiness (1 = Good, 0 = Bad).

## Workflow

### 1. Data Loading and Exploration
- Downloaded the German Credit dataset.
- Inspected dataset structure and variable types.
- Summarized key statistics of numerical and categorical variables.

### 2. Data Cleaning
- Renamed column names to ensure consistency and compatibility.
- Converted relevant columns to factors for proper handling in models.
- Verified the absence of missing values in the dataset.

### 3. Exploratory Data Analysis (EDA)
- Examined the distribution of the target variable (`RESPONSE`).
- Computed summary statistics (mean, standard deviation) for numerical variables.
- Visualized relationships between key features (e.g., `DURATION`, `AMOUNT`) and the response variable using boxplots.

### 4. Modeling
- **Decision Tree**: Built using the `rpart` package with Gini impurity as the splitting criterion.
- **Random Forest**: Constructed using the `randomForest` package with hyperparameters optimized for interpretability and accuracy.

### 5. Evaluation
- Split the dataset into training (70%) and testing (30%) subsets.
- Evaluated models using accuracy metrics on the test data.
- Compared feature importance for both models.

## Results
- **Decision Tree Accuracy**: ~71.2%
- **Random Forest Accuracy**: ~74.8%
- **Key Predictors**:
  - Checking account status (`CHK_ACCT`)
  - Credit amount (`AMOUNT`)
  - Credit history (`HISTORY`)
  - Duration of credit (`DURATION`)

## Tools and Libraries
- **R Libraries**:
  - `readxl`: For loading Excel data.
  - `ggplot2`: For data visualization.
  - `rpart` and `rpart.plot`: For building and visualizing Decision Trees.
  - `randomForest`: For Random Forest modeling and feature importance analysis.
  - `dplyr`: For data manipulation.
  - `kableExtra`: For creating professional-looking tables in R Markdown.
  - `gridExtra`: For arranging multiple plots.

## How to Run the Analysis
1. Clone this repository:
   ```bash
   git clone <https://github.com/stefanyayala/Machine-Learning-Projects>
   ```
2. Install required R libraries:
   ```R
   install.packages(c("readxl", "ggplot2", "rpart", "rpart.plot", "randomForest", "dplyr", "kableExtra", "gridExtra"))
   ```
3. Open the `german-credit-prediction.Rmd` file in RStudio.
4. Run all code chunks or knit the file to HTML/PDF.

## Future Work
- **Hyperparameter Tuning**: Optimize model parameters to enhance performance.
- **Feature Engineering**: Create additional features to capture complex relationships.
- **Expand Model Variety**: Explore other algorithms like Gradient Boosting or XGBoost.
- **Explainability**: Use SHAP values or LIME to improve model interpretability.

