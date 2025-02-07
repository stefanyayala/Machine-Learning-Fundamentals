Carseats Data Analysis
================
Stefany Ayala

## Data Preparation

``` r
# Define the URL of the Rdata file
url <- "https://drive.google.com/uc?id=16-zxtGm7-qYcjEKuxbLpZ3-nLYW-WAzX&export=download"

# Define a temporary file path to store the downloaded Rdata file
temp_file <- tempfile(fileext = ".Rdata")

# Download the Rdata file
download.file(url, destfile = temp_file, mode = "wb")  # Use mode="wb" for binary files

# Load the Rdata file into the environment
load(temp_file)

# Assign the dataset to a variable
df <- Data

# Display the first few rows of the dataset
head(df)
```

    ##     CompPrice Income Advertising Population Price ShelveLoc Age Education Urban
    ## 61        122    102          19        469   123       Bad  29        13   Yes
    ## 221       131    120          15        262   124    Medium  30        10   Yes
    ## 308       138     92           0         13   120       Bad  61        12   Yes
    ## 117       135     75           0        202   128    Medium  80        10    No
    ## 242       136     63           0        160    94    Medium  38        12   Yes
    ## 19        110    110           0        408    68      Good  46        17    No
    ##      US Target
    ## 61  Yes    YES
    ## 221 Yes    YES
    ## 308  No     No
    ## 117  No     No
    ## 242  No    YES
    ## 19  Yes    YES

## Model Building and Evaluation

``` r
# Load required libraries
library(randomForest)
library(klaR)
library(caret)
library(ROCR)
library(e1071)
library(rpart)
library(MASS)
library(KernelKnn)
library(FNN)
```

### Part I: Gain Chart

#### 1. Build Random Forest and Naïve Bayes models to predict the variable `Target`

``` r
set.seed(123)  # Set seed for reproducibility

# Split the data into training and testing sets (80% training, 20% testing)
index <- sample(2, nrow(df), replace = TRUE, prob = c(0.8, 0.2))
train_data <- df[index == 1,]
test_data <- df[index == 2,]

# Random Forest Model
rf <- randomForest(Target ~ ., data = train_data, ntree = 300, proximity = TRUE,
                   replace = TRUE, importance = TRUE)

# Naive Bayes Model
nb <- naiveBayes(Target ~ ., data = train_data)
nb_score <- predict(nb, test_data, type = 'raw')
```

#### 2. Gain Chart for `Target = Yes`

``` r
# Random Forest Prediction
rff <- predict(rf, test_data, type = 'vote')
score <- rff[, 2]

# Naive Bayes Prediction
pred_rf <- prediction(score, test_data$Target)
perf_rf <- performance(pred_rf, "tpr", "rpp")

pred_nb <- prediction(nb_score[, 2], test_data$Target)
perf_nb <- performance(pred_nb, "tpr", "fpr")

# Extract gain values for Naive Bayes
gain.x.nb <- unlist(slot(perf_nb, 'x.values'))
gain.y.nb <- unlist(slot(perf_nb, 'y.values'))

# Plot Gain Chart
plot(perf_rf, col = 'red', lty = 1, lwd = 2, main = "Gain Chart for RF and Naive Bayes Models")
lines(x = gain.x.nb, y = gain.y.nb, col = "orange", lwd = 2)
legend("bottomright", legend = c("Random Forest", "Naive Bayes"), lty = c(1, 1),
       col = c("red", "orange"), bty = "n", cex = 0.75)
```

![](C:/Users/nko16/DataGripProjects/ML-Projects/carseats-prediction_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

**Conclusion**: The Naive Bayes model is preferable based on the higher
area under the curve indicating better model performance.

#### 3. Gain Chart for `Target = No`

``` r
# Random Forest Prediction for 'Target = No'
rff <- predict(rf, test_data, type = 'vote')
score <- rff[, 1]

# Naive Bayes Prediction for 'Target = No'
pred_rf <- prediction(score, test_data$Target)
perf_rf <- performance(pred_rf, "tpr", "rpp")

pred_nb <- prediction(nb_score[, 1], test_data$Target)
perf_nb <- performance(pred_nb, "tpr", "fpr")

# Extract gain values for Naive Bayes
gain.x.nb <- unlist(slot(perf_nb, 'x.values'))
gain.y.nb <- unlist(slot(perf_nb, 'y.values'))

# Plot Gain Chart for 'Target = No'
plot(perf_rf, col = 'red', lty = 1, lwd = 2, main = "Gain Chart for RF and Naive Bayes Models")
lines(x = gain.x.nb, y = gain.y.nb, col = "orange", lwd = 2)
legend("bottomright", legend = c("Random Forest", "Naive Bayes"), lty = c(1, 1),
       col = c("red", "orange"), bty = "n", cex = 0.75)
```

![](C:/Users/nko16/DataGripProjects/ML-Projects/carseats-prediction_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

#### 4. Build a Decision Tree Model

``` r
# Decision Tree Model
dtree <- rpart(Target ~ ., data = train_data, method = "class")
```

#### 5. Model Evaluation using Confusion Matrix

``` r
# Evaluate Random Forest Model
confusionMatrix(predict(rf, type = "class", newdata = test_data),
                test_data$Target, positive = "YES")
```

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction No YES
    ##        No  40  11
    ##        YES  7  17
    ##                                           
    ##                Accuracy : 0.76            
    ##                  95% CI : (0.6475, 0.8511)
    ##     No Information Rate : 0.6267          
    ##     P-Value [Acc > NIR] : 0.01003         
    ##                                           
    ##                   Kappa : 0.4718          
    ##                                           
    ##  Mcnemar's Test P-Value : 0.47950         
    ##                                           
    ##             Sensitivity : 0.6071          
    ##             Specificity : 0.8511          
    ##          Pos Pred Value : 0.7083          
    ##          Neg Pred Value : 0.7843          
    ##              Prevalence : 0.3733          
    ##          Detection Rate : 0.2267          
    ##    Detection Prevalence : 0.3200          
    ##       Balanced Accuracy : 0.7291          
    ##                                           
    ##        'Positive' Class : YES             
    ## 

``` r
# Evaluate Naive Bayes Model
confusionMatrix(predict(nb, type = "class", newdata = test_data),
                test_data$Target, positive = "YES")
```

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction No YES
    ##        No  41  12
    ##        YES  6  16
    ##                                           
    ##                Accuracy : 0.76            
    ##                  95% CI : (0.6475, 0.8511)
    ##     No Information Rate : 0.6267          
    ##     P-Value [Acc > NIR] : 0.01003         
    ##                                           
    ##                   Kappa : 0.4639          
    ##                                           
    ##  Mcnemar's Test P-Value : 0.23859         
    ##                                           
    ##             Sensitivity : 0.5714          
    ##             Specificity : 0.8723          
    ##          Pos Pred Value : 0.7273          
    ##          Neg Pred Value : 0.7736          
    ##              Prevalence : 0.3733          
    ##          Detection Rate : 0.2133          
    ##    Detection Prevalence : 0.2933          
    ##       Balanced Accuracy : 0.7219          
    ##                                           
    ##        'Positive' Class : YES             
    ## 

``` r
# Evaluate Decision Tree Model
confusionMatrix(predict(dtree, type = "class", newdata = test_data),
                test_data$Target, positive = "YES")
```

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction No YES
    ##        No  36   8
    ##        YES 11  20
    ##                                          
    ##                Accuracy : 0.7467         
    ##                  95% CI : (0.633, 0.8401)
    ##     No Information Rate : 0.6267         
    ##     P-Value [Acc > NIR] : 0.01918        
    ##                                          
    ##                   Kappa : 0.4701         
    ##                                          
    ##  Mcnemar's Test P-Value : 0.64636        
    ##                                          
    ##             Sensitivity : 0.7143         
    ##             Specificity : 0.7660         
    ##          Pos Pred Value : 0.6452         
    ##          Neg Pred Value : 0.8182         
    ##              Prevalence : 0.3733         
    ##          Detection Rate : 0.2667         
    ##    Detection Prevalence : 0.4133         
    ##       Balanced Accuracy : 0.7401         
    ##                                          
    ##        'Positive' Class : YES            
    ## 

**Conclusion**: Based on accuracy, both the Random Forest and Naive
Bayes models are preferable, as they have similar performance.

#### 6. Model Preference Based on Cost of Misclassification

In the case where misclassifying stores with low sales has a higher cost
than misclassifying stores with high sales, the **Random Forest model**
is preferred. This is because Random Forest is better at minimizing
false negatives, which can help reduce the cost of misclassifying
low-sales stores as high-sales ones.

#### 7. Model Preference for Classifying High Sales Stores

If the priority is correctly classifying stores with high sales, the
**Naive Bayes model** is preferable, as it performs better in terms of
precision for classifying “high” sales stores.

------------------------------------------------------------------------

### Part II: K-Nearest Neighbors (KNN)

#### 1. Prepare the Dataset for KNN

``` r
# Min-max normalization function
normalize <- function(x) {
  num <- x - min(x)
  denom <- max(x) - min(x)
  return (num / denom)
}

# Apply normalization to the first 5 columns of the dataset
df_norm <- as.data.frame(lapply(df[1:5], normalize))
```

#### 2. Build a KNN Model with k = 3

``` r
# Split normalized data into training and test sets
df.training <- df_norm[index == 1, 1:5]
df.test <- df_norm[index == 2, 1:5]
df.trainLabels <- df[index == 1, 11]
df.testLabels <- df[index == 2, 11]

# Build KNN model with k=3
df_pred_k3 <- knn(train = df.training, test = df.test, cl = df.trainLabels, k = 3, prob = TRUE)
```

#### 3. Build a KNN Model with k = 1

``` r
# Build KNN model with k=1
df_pred_k1 <- knn(train = df.training, test = df.test, cl = df.trainLabels, k = 1, prob = TRUE)
```

#### 4. Evaluate the Models Based on Accuracy

``` r
# Train KNN model using caret package for k values 1 to 3
model <- train(Target ~ ., data = df, method = 'knn', tuneGrid = expand.grid(k = 1:3), metric = 'Accuracy')

# Display the model results
model
```

    ## k-Nearest Neighbors 
    ## 
    ## 400 samples
    ##  10 predictor
    ##   2 classes: 'No', 'YES' 
    ## 
    ## No pre-processing
    ## Resampling: Bootstrapped (25 reps) 
    ## Summary of sample sizes: 400, 400, 400, 400, 400, 400, ... 
    ## Resampling results across tuning parameters:
    ## 
    ##   k  Accuracy   Kappa    
    ##   1  0.5840003  0.1392976
    ##   2  0.5749309  0.1134473
    ##   3  0.5871094  0.1375068
    ## 
    ## Accuracy was used to select the optimal model using the largest value.
    ## The final value used for the model was k = 3.

**Conclusion**: Based on accuracy, it is better to use **k=3** as the
optimal number of neighbors for the KNN model.

\`\`\`
