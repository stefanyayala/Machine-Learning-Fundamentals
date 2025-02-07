Pima Indian Diabetes Analysis
================
Stefany Ayala

# Introduction

The objective of this analysis is to explore, clean, and prepare the
**Pima Indian Diabetes dataset** for modeling. This report demonstrates
the application of data cleaning, visualization, and machine learning
techniques in R. Additionally, we compare the performance of two
algorithms to showcase modeling versatility.

# 1. Data Loading

We first load the data directly from a URL into R and inspect its
structure.

``` r
# Load necessary libraries
library(ggplot2)
library(OneR)
library(rpart)

# Load the dataset
url <- "https://drive.google.com/uc?id=1MvxSOTYHknWFIukVGaEzR79-cLX_JHMC&export=download"
diabetes <- read.csv(url)

# Inspect dataset structure
dim(diabetes)  # Dimensions of the dataset
```

    ## [1] 768   9

``` r
attributes(diabetes)  # Attributes of the dataset
```

    ## $names
    ## [1] "Number.of.times.pregnant"                                                
    ## [2] "Plasma.glucose.concentration.a.2.hours.in.an.oral.glucose.tolerance.test"
    ## [3] "Diastolic.blood.pressure"                                                
    ## [4] "Triceps.skin.fold.thickness"                                             
    ## [5] "X2.Hour.serum.insulin"                                                   
    ## [6] "Body.mass.index"                                                         
    ## [7] "Diabetes.pedigree.function"                                              
    ## [8] "Age"                                                                     
    ## [9] "Class.variable"                                                          
    ## 
    ## $class
    ## [1] "data.frame"
    ## 
    ## $row.names
    ##   [1]   1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17  18
    ##  [19]  19  20  21  22  23  24  25  26  27  28  29  30  31  32  33  34  35  36
    ##  [37]  37  38  39  40  41  42  43  44  45  46  47  48  49  50  51  52  53  54
    ##  [55]  55  56  57  58  59  60  61  62  63  64  65  66  67  68  69  70  71  72
    ##  [73]  73  74  75  76  77  78  79  80  81  82  83  84  85  86  87  88  89  90
    ##  [91]  91  92  93  94  95  96  97  98  99 100 101 102 103 104 105 106 107 108
    ## [109] 109 110 111 112 113 114 115 116 117 118 119 120 121 122 123 124 125 126
    ## [127] 127 128 129 130 131 132 133 134 135 136 137 138 139 140 141 142 143 144
    ## [145] 145 146 147 148 149 150 151 152 153 154 155 156 157 158 159 160 161 162
    ## [163] 163 164 165 166 167 168 169 170 171 172 173 174 175 176 177 178 179 180
    ## [181] 181 182 183 184 185 186 187 188 189 190 191 192 193 194 195 196 197 198
    ## [199] 199 200 201 202 203 204 205 206 207 208 209 210 211 212 213 214 215 216
    ## [217] 217 218 219 220 221 222 223 224 225 226 227 228 229 230 231 232 233 234
    ## [235] 235 236 237 238 239 240 241 242 243 244 245 246 247 248 249 250 251 252
    ## [253] 253 254 255 256 257 258 259 260 261 262 263 264 265 266 267 268 269 270
    ## [271] 271 272 273 274 275 276 277 278 279 280 281 282 283 284 285 286 287 288
    ## [289] 289 290 291 292 293 294 295 296 297 298 299 300 301 302 303 304 305 306
    ## [307] 307 308 309 310 311 312 313 314 315 316 317 318 319 320 321 322 323 324
    ## [325] 325 326 327 328 329 330 331 332 333 334 335 336 337 338 339 340 341 342
    ## [343] 343 344 345 346 347 348 349 350 351 352 353 354 355 356 357 358 359 360
    ## [361] 361 362 363 364 365 366 367 368 369 370 371 372 373 374 375 376 377 378
    ## [379] 379 380 381 382 383 384 385 386 387 388 389 390 391 392 393 394 395 396
    ## [397] 397 398 399 400 401 402 403 404 405 406 407 408 409 410 411 412 413 414
    ## [415] 415 416 417 418 419 420 421 422 423 424 425 426 427 428 429 430 431 432
    ## [433] 433 434 435 436 437 438 439 440 441 442 443 444 445 446 447 448 449 450
    ## [451] 451 452 453 454 455 456 457 458 459 460 461 462 463 464 465 466 467 468
    ## [469] 469 470 471 472 473 474 475 476 477 478 479 480 481 482 483 484 485 486
    ## [487] 487 488 489 490 491 492 493 494 495 496 497 498 499 500 501 502 503 504
    ## [505] 505 506 507 508 509 510 511 512 513 514 515 516 517 518 519 520 521 522
    ## [523] 523 524 525 526 527 528 529 530 531 532 533 534 535 536 537 538 539 540
    ## [541] 541 542 543 544 545 546 547 548 549 550 551 552 553 554 555 556 557 558
    ## [559] 559 560 561 562 563 564 565 566 567 568 569 570 571 572 573 574 575 576
    ## [577] 577 578 579 580 581 582 583 584 585 586 587 588 589 590 591 592 593 594
    ## [595] 595 596 597 598 599 600 601 602 603 604 605 606 607 608 609 610 611 612
    ## [613] 613 614 615 616 617 618 619 620 621 622 623 624 625 626 627 628 629 630
    ## [631] 631 632 633 634 635 636 637 638 639 640 641 642 643 644 645 646 647 648
    ## [649] 649 650 651 652 653 654 655 656 657 658 659 660 661 662 663 664 665 666
    ## [667] 667 668 669 670 671 672 673 674 675 676 677 678 679 680 681 682 683 684
    ## [685] 685 686 687 688 689 690 691 692 693 694 695 696 697 698 699 700 701 702
    ## [703] 703 704 705 706 707 708 709 710 711 712 713 714 715 716 717 718 719 720
    ## [721] 721 722 723 724 725 726 727 728 729 730 731 732 733 734 735 736 737 738
    ## [739] 739 740 741 742 743 744 745 746 747 748 749 750 751 752 753 754 755 756
    ## [757] 757 758 759 760 761 762 763 764 765 766 767 768

# 2. Data Exploration

## 2.1 Column Renaming

For better readability, we rename the columns.

``` r
# Rename columns
colnames(diabetes) <- c("pregnant", "glucose", "bp", "triceps", "insulin", "bmi",
    "diabetes", "age", "target")
```

## 2.2 Initial Data Insights

We explore the dataset’s variables with histograms and basic descriptive
statistics.

``` r
# Visualize data distributions
par(mfrow = c(3, 3))
for (col in names(diabetes)[-9]) {
    hist(diabetes[[col]], main = paste("Histogram of", toupper(col)), xlab = toupper(col))
}

# Summarize descriptive statistics
summary(diabetes)
```

    ##     pregnant         glucose            bp            triceps     
    ##  Min.   : 0.000   Min.   :  0.0   Min.   :  0.00   Min.   : 0.00  
    ##  1st Qu.: 1.000   1st Qu.: 99.0   1st Qu.: 62.00   1st Qu.: 0.00  
    ##  Median : 3.000   Median :117.0   Median : 72.00   Median :23.00  
    ##  Mean   : 3.845   Mean   :120.9   Mean   : 69.11   Mean   :20.54  
    ##  3rd Qu.: 6.000   3rd Qu.:140.2   3rd Qu.: 80.00   3rd Qu.:32.00  
    ##  Max.   :17.000   Max.   :199.0   Max.   :122.00   Max.   :99.00  
    ##     insulin           bmi           diabetes           age       
    ##  Min.   :  0.0   Min.   : 0.00   Min.   :0.0780   Min.   :21.00  
    ##  1st Qu.:  0.0   1st Qu.:27.30   1st Qu.:0.2437   1st Qu.:24.00  
    ##  Median : 30.5   Median :32.00   Median :0.3725   Median :29.00  
    ##  Mean   : 79.8   Mean   :31.99   Mean   :0.4719   Mean   :33.24  
    ##  3rd Qu.:127.2   3rd Qu.:36.60   3rd Qu.:0.6262   3rd Qu.:41.00  
    ##  Max.   :846.0   Max.   :67.10   Max.   :2.4200   Max.   :81.00  
    ##      target     
    ##  Min.   :0.000  
    ##  1st Qu.:0.000  
    ##  Median :0.000  
    ##  Mean   :0.349  
    ##  3rd Qu.:1.000  
    ##  Max.   :1.000

![](C:/Users/nko16/DataGripProjects/ML-Projects/pima-diabetes-classification_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

# 3. Data Cleaning

## 3.1 Detecting and Handling Outliers

Outliers are detected using Tukey’s method and replaced with NA.

``` r
# Handle outliers
clean_data <- diabetes
for (col in names(diabetes)[-9]) {
    outliers <- boxplot.stats(diabetes[[col]])$out
    clean_data[[col]] <- ifelse(diabetes[[col]] %in% outliers, NA, diabetes[[col]])
}
```

## 3.2 Imputing Missing Values

Missing values are replaced with the mean of each variable.

``` r
# Impute missing values with the mean
clean_data <- as.data.frame(lapply(clean_data, function(x) ifelse(is.na(x), mean(x,
    na.rm = TRUE), x)))

# Verify no missing values remain
sum(is.na(clean_data))
```

    ## [1] 0

# 4. Data Preparation

The cleaned dataset is split into training (80%) and testing (20%) sets.

``` r
# Split data
set.seed(123)
train_indices <- sample(1:nrow(clean_data), size = 0.8 * nrow(clean_data))
TrainData <- clean_data[train_indices, ]
TestData <- clean_data[-train_indices, ]
```

# 5. Modeling

Interpretation

- **OneR Model:**

This simple model provides interpretable rules based on a single
attribute, which can be ideal for quick insights. The model’s
performance metrics indicate its effectiveness in this context.

- **Decision Tree Model:**

The confusion matrix reveals the strengths and weaknesses in
classification. Decision Trees, though more complex, offer better
granularity in predictions.

By comparing the results of both models, we gain insights into the
trade-offs between model simplicity and performance.

## 5.1 OneR Model

We train a OneR model to identify the best attribute and evaluate its
performance.

``` r
# Train a OneR model
one_r_model <- OneR(target ~ ., data = TrainData, verbose = TRUE)
```

    ## 
    ##     Attribute Accuracy
    ## 1 * glucose   75.24%  
    ## 2   age       67.1%   
    ## 3   pregnant  65.8%   
    ## 4   bmi       65.47%  
    ## 5   insulin   65.15%  
    ## 6   bp        64.82%  
    ## 6   triceps   64.82%  
    ## 6   diabetes  64.82%  
    ## ---
    ## Chosen attribute due to accuracy
    ## and ties method (if applicable): '*'

``` r
# Summarize the model
summary(one_r_model)
```

    ## 
    ## Call:
    ## OneR.formula(formula = target ~ ., data = TrainData, verbose = TRUE)
    ## 
    ## Rules:
    ## If glucose = (55.9,84.6] then target = 0
    ## If glucose = (84.6,113]  then target = 0
    ## If glucose = (113,142]   then target = 0
    ## If glucose = (142,170]   then target = 1
    ## If glucose = (170,199]   then target = 1
    ## 
    ## Accuracy:
    ## 462 of 614 instances classified correctly (75.24%)
    ## 
    ## Contingency table:
    ##       glucose
    ## target (55.9,84.6] (84.6,113] (113,142] (142,170] (170,199] Sum
    ##    0          * 51      * 178     * 129        31         9 398
    ##    1             1         36        75      * 56      * 48 216
    ##    Sum          52        214       204        87        57 614
    ## ---
    ## Maximum in each column: '*'
    ## 
    ## Pearson's Chi-squared test:
    ## X-squared = 149.67, df = 4, p-value < 2.2e-16

``` r
# Predict on test data
one_r_predictions <- predict(one_r_model, TestData)

# Evaluate the model
one_r_eval <- eval_model(one_r_predictions, TestData)
```

    ## 
    ## Confusion matrix (absolute):
    ##           Actual
    ## Prediction   0   1 UNSEEN Sum
    ##     0       87  28      0 115
    ##     1       14  24      0  38
    ##     UNSEEN   1   0      0   1
    ##     Sum    102  52      0 154
    ## 
    ## Confusion matrix (relative):
    ##           Actual
    ## Prediction    0    1 UNSEEN  Sum
    ##     0      0.56 0.18   0.00 0.75
    ##     1      0.09 0.16   0.00 0.25
    ##     UNSEEN 0.01 0.00   0.00 0.01
    ##     Sum    0.66 0.34   0.00 1.00
    ## 
    ## Accuracy:
    ## 0.7208 (111/154)
    ## 
    ## Error rate:
    ## 0.2792 (43/154)
    ## 
    ## Error rate reduction (vs. base rate):
    ## 0.1731 (p-value = 0.07215)

``` r
one_r_eval
```

    ## $correct_instances
    ## [1] 111
    ## 
    ## $total_instances
    ## [1] 154
    ## 
    ## $conf_matrix
    ##           Actual
    ## Prediction  0  1 UNSEEN
    ##     0      87 28      0
    ##     1      14 24      0
    ##     UNSEEN  1  0      0

## 5.2 Decision Tree Model

As a comparison, we train a Decision Tree model and evaluate its
performance.

``` r
# Train a decision tree model
tree_model <- rpart(target ~ ., data = TrainData, method = "class")

# Predict on test data (specifying 'type = 'class'' to get class labels)
tree_predictions <- predict(tree_model, TestData, type = "class")

# Confusion matrix
confusion_matrix <- table(Predicted = tree_predictions, Actual = TestData$target)
confusion_matrix
```

    ##          Actual
    ## Predicted  0  1
    ##         0 81 24
    ##         1 21 28

# 6. Results and Evaluation

## 6.1 OneR Model Results

The OneR model was trained on the dataset to identify the best single
predictor of the target variable. The OneR model achieves an accuracy of
**75.24%** based on the rule for the `glucose` variable. The model
divides the `glucose` variable into ranges and predicts the target
variable based on these ranges.

The confusion matrix is as follows:

``` r
# Display evaluation metrics
one_r_eval
```

    ## $correct_instances
    ## [1] 111
    ## 
    ## $total_instances
    ## [1] 154
    ## 
    ## $conf_matrix
    ##           Actual
    ## Prediction  0  1 UNSEEN
    ##     0      87 28      0
    ##     1      14 24      0
    ##     UNSEEN  1  0      0

From the confusion matrix, we can see that the OneR model has some false
positives and false negatives, but it is still fairly accurate for this
task.

## 6.2 Decision Tree Model Results

To compare the performance of a more complex algorithm, a Decision Tree
model was trained. Its predictions on the test data are evaluated using
a confusion matrix:

``` r
# Confusion matrix for Decision Tree model
confusion_matrix <- table(tree_predictions, TestData$target)

# Display confusion matrix
confusion_matrix
```

    ##                 
    ## tree_predictions  0  1
    ##                0 81 24
    ##                1 21 28

The Decision Tree model is capable of capturing more complex
relationships in the data compared to the OneR model, though it also has
some misclassifications.

# Final thoughs

Both models show reasonable accuracy, but the OneR model is simpler and
more interpretable. The Decision Tree model, while more complex, may be
more suitable for capturing non-linear patterns. Further
experimentation, such as cross-validation and hyperparameter tuning,
could lead to improved performance.
