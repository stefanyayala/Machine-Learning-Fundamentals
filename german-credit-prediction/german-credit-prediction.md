Predicting Customer Credit Risk with Decision Trees and Random Forests
================
Stefany Ayala

## Objective

Explore and clean the German Credit dataset, perform exploratory data
analysis (EDA), and build classification models (Decision Tree and
Random Forest) to predict creditworthiness (Good vs. Bad).

## Libraries

``` r
# Load necessary libraries
library(readxl)
library(ggplot2)
library(rpart)
library(rpart.plot)
library(randomForest)
library(dplyr)
library(knitr)
library(kableExtra)
library(gridExtra)

# Set global options for the document
knitr::opts_chunk$set(echo = TRUE, message = FALSE, warning = FALSE)
```

### Data Loading

``` r
# Define data source and local file
url <- "https://drive.google.com/uc?id=1vG_m6jCz-nwYwowTbMxwtVrmGtSTUhcH&export=download"
local_file <- "German_Credit.xls"

# Download and load data
if (!file.exists(local_file)) {
  # Download the dataset if it does not already exist locally
  download.file(url, destfile = local_file, mode = "wb")
}

# Read the dataset into an R data frame
German_Credit <- read_excel(local_file)

# Inspect the structure of the dataset
str(German_Credit)
```

    ## tibble [1,000 × 32] (S3: tbl_df/tbl/data.frame)
    ##  $ OBS#            : num [1:1000] 1 2 3 4 5 6 7 8 9 10 ...
    ##  $ CHK_ACCT        : num [1:1000] 0 1 3 0 0 3 3 1 3 1 ...
    ##  $ DURATION        : num [1:1000] 6 48 12 42 24 36 24 36 12 30 ...
    ##  $ HISTORY         : num [1:1000] 4 2 4 2 3 2 2 2 2 4 ...
    ##  $ NEW_CAR         : num [1:1000] 0 0 0 0 1 0 0 0 0 1 ...
    ##  $ USED_CAR        : num [1:1000] 0 0 0 0 0 0 0 1 0 0 ...
    ##  $ FURNITURE       : num [1:1000] 0 0 0 1 0 0 1 0 0 0 ...
    ##  $ RADIO/TV        : num [1:1000] 1 1 0 0 0 0 0 0 1 0 ...
    ##  $ EDUCATION       : num [1:1000] 0 0 1 0 0 1 0 0 0 0 ...
    ##  $ RETRAINING      : num [1:1000] 0 0 0 0 0 0 0 0 0 0 ...
    ##  $ AMOUNT          : num [1:1000] 1169 5951 2096 7882 4870 ...
    ##  $ SAV_ACCT        : num [1:1000] 4 0 0 0 0 4 2 0 3 0 ...
    ##  $ EMPLOYMENT      : num [1:1000] 4 2 3 3 2 2 4 2 3 0 ...
    ##  $ INSTALL_RATE    : num [1:1000] 4 2 2 2 3 2 3 2 2 4 ...
    ##  $ MALE_DIV        : num [1:1000] 0 0 0 0 0 0 0 0 1 0 ...
    ##  $ MALE_SINGLE     : num [1:1000] 1 0 1 1 1 1 1 1 0 0 ...
    ##  $ MALE_MAR_or_WID : num [1:1000] 0 0 0 0 0 0 0 0 0 1 ...
    ##  $ CO-APPLICANT    : num [1:1000] 0 0 0 0 0 0 0 0 0 0 ...
    ##  $ GUARANTOR       : num [1:1000] 0 0 0 1 0 0 0 0 0 0 ...
    ##  $ PRESENT_RESIDENT: num [1:1000] 4 2 3 4 4 4 4 2 4 2 ...
    ##  $ REAL_ESTATE     : num [1:1000] 1 1 1 0 0 0 0 0 1 0 ...
    ##  $ PROP_UNKN_NONE  : num [1:1000] 0 0 0 0 1 1 0 0 0 0 ...
    ##  $ AGE             : num [1:1000] 67 22 49 45 53 35 53 35 61 28 ...
    ##  $ OTHER_INSTALL   : num [1:1000] 0 0 0 0 0 0 0 0 0 0 ...
    ##  $ RENT            : num [1:1000] 0 0 0 0 0 0 0 1 0 0 ...
    ##  $ OWN_RES         : num [1:1000] 1 1 1 0 0 0 1 0 1 1 ...
    ##  $ NUM_CREDITS     : num [1:1000] 2 1 1 1 2 1 1 1 1 2 ...
    ##  $ JOB             : num [1:1000] 2 2 1 2 2 1 2 3 1 3 ...
    ##  $ NUM_DEPENDENTS  : num [1:1000] 1 1 2 2 2 2 1 1 1 1 ...
    ##  $ TELEPHONE       : num [1:1000] 1 0 0 0 0 1 0 1 0 0 ...
    ##  $ FOREIGN         : num [1:1000] 0 0 0 0 0 0 0 0 0 0 ...
    ##  $ RESPONSE        : num [1:1000] 1 0 1 1 0 1 1 1 1 0 ...

``` r
kable(summary(German_Credit), format = "latex") %>%
  kable_styling() %>%
  scroll_box(width = "100%")
```

### Functions

``` r
# Function for data cleaning
clean_data <- function(data) {
  data <- data %>%
    rename_all(~ gsub("[^a-zA-Z0-9_]+", "_", .)) %>% # Sanitize column names
    mutate(across(c("CHK_ACCT", "HISTORY", "SAV_ACCT", "EMPLOYMENT", "PRESENT_RESIDENT", "JOB", "RESPONSE"), as.factor))
  return(data)
}

# Function for EDA
eda <- function(data) {
  # Print summary statistics
  print(summary(data))
  response_proportion <- prop.table(table(data$RESPONSE))
  kable(as.data.frame(response_proportion), col.names = c("Response", "Proportion"), format = "latex") %>%
    kable_styling(full_width = FALSE)

  # Calculate summary stats for numeric variables
  numeric_vars <- c("DURATION", "AMOUNT", "AGE", "INSTALL_RATE", "NUM_DEPENDENTS")
  summary_stats <- data %>%
    summarise(across(all_of(numeric_vars), list(mean = ~mean(.x, na.rm = TRUE), sd = ~sd(.x, na.rm = TRUE))))
  kable(summary_stats, format = "latex") %>% kable_styling(full_width = TRUE)

  # Generate boxplots for key relationships
  p1 <- ggplot(data, aes(x = factor(RESPONSE), y = DURATION)) +
    geom_boxplot() +
    labs(x = "Response", y = "Duration", title = "Duration by Response")
  p2 <- ggplot(data, aes(x = factor(RESPONSE), y = AMOUNT)) +
    geom_boxplot() +
    labs(x = "Response", y = "Amount", title = "Amount by Response")

  # Arrange the boxplots side by side
  gridExtra::grid.arrange(p1, p2, ncol = 2)
}

# Function for model training and evaluation
train_evaluate_models <- function(train_data, test_data) {
  # Decision Tree Model
  decision_tree <- rpart(RESPONSE ~ ., data = train_data, method = "class", parms = list(split = "gini"),
                         control = rpart.control(xval = 5, cp = 0.01))
  rpart.plot(decision_tree, main = "Decision Tree")

  # Random Forest Model
  random_forest <- randomForest(RESPONSE ~ ., data = train_data, ntree = 200, mtry = 5, importance = TRUE)

  # Display variable importance for the Random Forest model
  randomForest::varImpPlot(random_forest, cex = 0.5, main = "Random Forest Variable Importance")

  # Model Evaluation
  decision_tree_predictions <- predict(decision_tree, test_data, type = "class")
  random_forest_predictions <- predict(random_forest, test_data, type = "class")

  decision_tree_accuracy <- mean(decision_tree_predictions == test_data$RESPONSE)
  random_forest_accuracy <- mean(random_forest_predictions == test_data$RESPONSE)

  # Print accuracy metrics
  cat("Decision Tree Accuracy:", decision_tree_accuracy, "\n")
  cat("Random Forest Accuracy:", random_forest_accuracy, "\n")
}
```

### Data Cleaning

``` r
# Clean the dataset using the custom function
German_Credit <- clean_data(German_Credit)

# Check for missing values
missing_vals <- sum(is.na(German_Credit))
cat("Number of missing values:", missing_vals, "\n")
```

    ## Number of missing values: 0

### Exploratory Data Analysis (EDA)

``` r
# Perform EDA using the custom function
eda(German_Credit)
```

    ##       OBS_        CHK_ACCT    DURATION    HISTORY    NEW_CAR     
    ##  Min.   :   1.0   0:274    Min.   : 4.0   0: 40   Min.   :0.000  
    ##  1st Qu.: 250.8   1:269    1st Qu.:12.0   1: 49   1st Qu.:0.000  
    ##  Median : 500.5   2: 63    Median :18.0   2:530   Median :0.000  
    ##  Mean   : 500.5   3:394    Mean   :20.9   3: 88   Mean   :0.234  
    ##  3rd Qu.: 750.2            3rd Qu.:24.0   4:293   3rd Qu.:0.000  
    ##  Max.   :1000.0            Max.   :72.0           Max.   :1.000  
    ##     USED_CAR       FURNITURE        RADIO_TV      EDUCATION      RETRAINING   
    ##  Min.   :0.000   Min.   :0.000   Min.   :0.00   Min.   :0.00   Min.   :0.000  
    ##  1st Qu.:0.000   1st Qu.:0.000   1st Qu.:0.00   1st Qu.:0.00   1st Qu.:0.000  
    ##  Median :0.000   Median :0.000   Median :0.00   Median :0.00   Median :0.000  
    ##  Mean   :0.103   Mean   :0.181   Mean   :0.28   Mean   :0.05   Mean   :0.097  
    ##  3rd Qu.:0.000   3rd Qu.:0.000   3rd Qu.:1.00   3rd Qu.:0.00   3rd Qu.:0.000  
    ##  Max.   :1.000   Max.   :1.000   Max.   :1.00   Max.   :1.00   Max.   :1.000  
    ##      AMOUNT      SAV_ACCT EMPLOYMENT  INSTALL_RATE      MALE_DIV   
    ##  Min.   :  250   0:603    0: 62      Min.   :1.000   Min.   :0.00  
    ##  1st Qu.: 1366   1:103    1:172      1st Qu.:2.000   1st Qu.:0.00  
    ##  Median : 2320   2: 63    2:339      Median :3.000   Median :0.00  
    ##  Mean   : 3271   3: 48    3:174      Mean   :2.973   Mean   :0.05  
    ##  3rd Qu.: 3972   4:183    4:253      3rd Qu.:4.000   3rd Qu.:0.00  
    ##  Max.   :18424                       Max.   :4.000   Max.   :1.00  
    ##   MALE_SINGLE    MALE_MAR_or_WID  CO_APPLICANT     GUARANTOR    
    ##  Min.   :0.000   Min.   :0.000   Min.   :0.000   Min.   :0.000  
    ##  1st Qu.:0.000   1st Qu.:0.000   1st Qu.:0.000   1st Qu.:0.000  
    ##  Median :1.000   Median :0.000   Median :0.000   Median :0.000  
    ##  Mean   :0.548   Mean   :0.092   Mean   :0.041   Mean   :0.052  
    ##  3rd Qu.:1.000   3rd Qu.:0.000   3rd Qu.:0.000   3rd Qu.:0.000  
    ##  Max.   :1.000   Max.   :1.000   Max.   :1.000   Max.   :1.000  
    ##  PRESENT_RESIDENT  REAL_ESTATE    PROP_UNKN_NONE       AGE       
    ##  1:130            Min.   :0.000   Min.   :0.000   Min.   :19.00  
    ##  2:308            1st Qu.:0.000   1st Qu.:0.000   1st Qu.:27.00  
    ##  3:149            Median :0.000   Median :0.000   Median :33.00  
    ##  4:413            Mean   :0.282   Mean   :0.154   Mean   :35.55  
    ##                   3rd Qu.:1.000   3rd Qu.:0.000   3rd Qu.:42.00  
    ##                   Max.   :1.000   Max.   :1.000   Max.   :75.00  
    ##  OTHER_INSTALL        RENT          OWN_RES       NUM_CREDITS    JOB    
    ##  Min.   :0.000   Min.   :0.000   Min.   :0.000   Min.   :1.000   0: 22  
    ##  1st Qu.:0.000   1st Qu.:0.000   1st Qu.:0.000   1st Qu.:1.000   1:200  
    ##  Median :0.000   Median :0.000   Median :1.000   Median :1.000   2:630  
    ##  Mean   :0.186   Mean   :0.179   Mean   :0.713   Mean   :1.407   3:148  
    ##  3rd Qu.:0.000   3rd Qu.:0.000   3rd Qu.:1.000   3rd Qu.:2.000          
    ##  Max.   :1.000   Max.   :1.000   Max.   :1.000   Max.   :4.000          
    ##  NUM_DEPENDENTS    TELEPHONE        FOREIGN      RESPONSE
    ##  Min.   :1.000   Min.   :0.000   Min.   :0.000   0:300   
    ##  1st Qu.:1.000   1st Qu.:0.000   1st Qu.:0.000   1:700   
    ##  Median :1.000   Median :0.000   Median :0.000           
    ##  Mean   :1.155   Mean   :0.404   Mean   :0.037           
    ##  3rd Qu.:1.000   3rd Qu.:1.000   3rd Qu.:0.000           
    ##  Max.   :2.000   Max.   :1.000   Max.   :1.000

![](C:/Users/nko16/DataGripProjects/ML-Projects/german-credit-prediction_files/figure-gfm/eda-1.png)<!-- -->

### Modeling

``` r
# Split the data into training and testing sets
set.seed(1234)  # Set seed for reproducibility
index <- sample(2, nrow(German_Credit), replace = TRUE, prob = c(0.7, 0.3))
train_data <- German_Credit[index == 1, ]
test_data <- German_Credit[index == 2, ]

# Train and evaluate models using the custom function
train_evaluate_models(train_data, test_data)
```

![](C:/Users/nko16/DataGripProjects/ML-Projects/german-credit-prediction_files/figure-gfm/modeling-1.png)<!-- -->![](C:/Users/nko16/DataGripProjects/ML-Projects/german-credit-prediction_files/figure-gfm/modeling-2.png)<!-- -->

    ## Decision Tree Accuracy: 0.7119205 
    ## Random Forest Accuracy: 0.7483444

### Conclusion

    ## - The Random Forest model showed higher predictive power compared to the Decision Tree.

    ## - Key predictive features include Checking Account and Credit Amount.

    ## - Future improvements could involve hyperparameter tuning and expanding feature engineering.
