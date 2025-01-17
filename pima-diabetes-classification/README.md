# Pima Indian Diabetes Classification

This project demonstrates a machine learning workflow using the **Pima Indian Diabetes dataset**, focusing on **data preprocessing**, **exploratory data analysis (EDA)**, and building **classification models** to predict the likelihood of diabetes. The project is implemented in R and highlights key concepts in supervised machine learning.

---

## Dataset Description
The Pima Indian Diabetes dataset consists of diagnostic measurements collected from 768 female patients of at least 21 years of age, of which 9 attributes are used to predict diabetes. The `target` variable indicates whether a patient has diabetes (`1`) or not (`0`).

### Features:
1. **pregnant**: Number of times pregnant  
2. **glucose**: Plasma glucose concentration after a 2-hour oral glucose tolerance test  
3. **bp**: Diastolic blood pressure (mm Hg)  
4. **triceps**: Triceps skinfold thickness (mm)  
5. **insulin**: 2-Hour serum insulin (mu U/ml)  
6. **bmi**: Body mass index (weight in kg/(height in m)^2)  
7. **diabetes**: Diabetes pedigree function  
8. **age**: Age in years  
9. **target**: Outcome variable (0 = No diabetes, 1 = Diabetes)

---

## Project Workflow
This project covers the following stages of machine learning:

### 1. **Data Preprocessing**
- Loading the dataset directly from a public link.
- Renaming columns for readability.
- Handling missing values and outliers:
  - Outliers are detected using Tukey's method.
  - Outliers are replaced with `NA` and then imputed with the mean of the respective columns.

### 2. **Exploratory Data Analysis (EDA)**
- Visualizing distributions using histograms to understand variable distributions and detect outliers.

### 3. **Data Splitting**
- Splitting the dataset into training (80%) and testing (20%) sets using random sampling.

### 4. **Modeling**
#### a. **OneR Model**
- A simple, interpretable classification model based on one variable.
- Selects the best predictor (glucose) using training data.

#### b. **Decision Tree**
- A more advanced model using the `rpart` package.
- Evaluates model performance using a confusion matrix.

### 5. **Model Evaluation**
- Accuracy and error rates are computed for both models.
- Comparison of results between OneR and the Decision Tree model.

---

## Key Machine Learning Concepts Covered
1. **Data Preprocessing**: Outlier detection and replacement, mean imputation for missing values.
2. **Supervised Learning**: Binary classification task.
3. **Model Selection**: Use of OneR and Decision Tree algorithms.
4. **Model Evaluation**: Confusion matrix and accuracy comparison.
5. **Feature Selection**: Identifying the most significant predictor for the target variable.

---

## Results
### OneR Model:
- **Best Predictor**: Glucose
- **Accuracy**: 75.24%

### Decision Tree Model:
- **Confusion Matrix**:
  ```
                  Actual
  Prediction      0   1
           0     81  24
           1     21  28
  ```
- **Performance**: Accuracy is slightly lower than the OneR model due to the simplicity of the dataset.

---

## Prerequisites
To run this project, you will need:
- R (version 4.0 or higher)
- RStudio (optional)
- Required R libraries:
  - `rpart` for Decision Tree
  - `OneR` for One Rule classification

---

## How to Run the Project
1. Clone the repository:  
   ```bash
   git clone https://github.com/stefanyayala/Machine-Learning-Fundamentals.git
   ```
2. Navigate to the project directory:  
   ```bash
   cd Machine-Learning-Fundamentals/pima-diabetes-classification
   ```
3. Open the R Markdown file in RStudio:  
   ```plaintext
   pima-diabetes-classification.Rmd
   ```
4. Knit the file to generate the HTML or PDF output.

---

## Insights
This project serves as a foundational exercise in supervised learning, providing hands-on experience in data preprocessing, EDA, and model evaluation. The simplicity of the dataset highlights the importance of feature selection and the trade-offs between model interpretability and complexity.

---

## Future Improvements
- Explore additional algorithms like Random Forest or Logistic Regression.
- Perform hyperparameter tuning for the Decision Tree model.
- Use cross-validation to improve model reliability.
