# Random Forest Classification



### Classification - Theory
Unlike regression where you predict a continuous number, you use classification to predict a category. There is a wide variety of classification applications from medicine to marketing.

Machine Learning Classification models:

* Logistic Regression
* K-NN
* SVM
* Kernel SVM
* Naive Bayes
* Decision Tree Classification
* Random Forest Classification

If your problem is linear, you should go for Logistic Regression or SVM.

If your problem is non linear, you should go for K-NN, Naive Bayes, Decision Tree or Random Forest. 

Model Selection with k-Fold Cross Validation.

From a business point of view, you would rather use:

* Logistic Regression or Naive Bayes when you want to rank your predictions by their probability. For example if you want to rank your customers from the highest probability that they buy a certain product, to the lowest probability. Eventually that allows you to target your marketing campaigns. For this type of business problem, you should use Logistic Regression if your problem is linear, and Naive Bayes if your problem is non linear.

* SVM when you want to predict to which segment your customers belong to. Segments can be any kind of segments, for example some market segments you identified earlier with clustering.

* Decision Tree when you want to have clear interpretation of your model results

* Random Forest when you are just looking for high performance with less need for interpretation.

### Random Forest Classification - Theory
Random Forest is a version of Ensemble Learning 

Random Forest = Team of Decision Tree Regression, each making a prediction --> average of all trees

* Pros Powerful and accurate, good performance on many problems, including non linear
* Cons: No interpretability, overfitting can easily occur, need to choose the number of trees

### Business Problem (Udemy)
Dataset includes variables on User ID, Gender, Age, Estimated Salary
One of the Social Media Pages Client, a big car company, has published an add on their site.
Social Media collected information which of their users has bought the car (1) or not (0)
Model should predict if there is a correlation between Age and Salary and the purchase behaviour.

Goal: to correctly predict if an individual user is buying the product or not

-- Importing dataset -- 

```r
dataset = read.csv('Social_Network_Ads.csv')
dataset = dataset[3:5]
```

-- Encoding the target feature as factor -- 

```r
dataset$Purchased = factor(dataset$Purchased, levels = c(0, 1))
```

-- Splitting the dataset into the Training set and Test set -- 

```r
library(caTools)
set.seed(123)
split = sample.split(dataset$Purchased, SplitRatio = 0.75)
training_set = subset(dataset, split == TRUE)
test_set = subset(dataset, split == FALSE)
```

-- Feature Scaling -- 

```r
training_set[-3] = scale(training_set[-3])
test_set[-3] = scale(test_set[-3])
# only necessary for high resolution plots
```

-- Fitting Classifier to the Training set -- 

```r
library(randomForest)
classifier = randomForest(x = training_set[-3],
                          y = training_set$Purchased,
                          ntree = 10)
# x = matrix of predictors/features (IV Age, Salary)
# y = response vector = DV (Purchased Column)
# ntree = number of trees 
```

-- Predicting the Test set results -- 

```r
y_pred = predict(classifier, newdata = test_set[-3])
```

-- Making the Confusion Matrix -- 

```r
cm = table(test_set[, 3], y_pred)
cm
```

```
##    y_pred
##      0  1
##   0 58  6
##   1  9 27
```

-- Visualising the Training set results -- 

```r
library(ElemStatLearn)
set = training_set
X1 = seq(min(set[, 1]) - 1, max(set[, 1]) + 1, by = 0.01)
X2 = seq(min(set[, 2]) - 1, max(set[, 2]) + 1, by = 0.01)
grid_set = expand.grid(X1, X2)
colnames(grid_set) = c('Age', 'EstimatedSalary')
y_grid = predict(classifier, newdata = grid_set)
plot(set[, -3],
     main = 'Random Forest Classification (Training set)',
     xlab = 'Age', ylab = 'Estimated Salary',
     xlim = range(X1), ylim = range(X2))
contour(X1, X2, matrix(as.numeric(y_grid), length(X1), length(X2)), add = TRUE)
points(grid_set, pch = '.', col = ifelse(y_grid == 1, 'springgreen3', 'tomato'))
points(set, pch = 21, bg = ifelse(set[, 3] == 1, 'green4', 'red3'))
```

![](RandomForestClassification_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

-- Visualising the Test set results -- 

```r
library(ElemStatLearn)
set = test_set
X1 = seq(min(set[, 1]) - 1, max(set[, 1]) + 1, by = 0.01)
X2 = seq(min(set[, 2]) - 1, max(set[, 2]) + 1, by = 0.01)
grid_set = expand.grid(X1, X2)
colnames(grid_set) = c('Age', 'EstimatedSalary')
y_grid = predict(classifier, newdata = grid_set)
plot(set[, -3],
     main = 'Random Forest Classification (Test set)',
     xlab = 'Age', ylab = 'Estimated Salary',
     xlim = range(X1), ylim = range(X2))
contour(X1, X2, matrix(as.numeric(y_grid), length(X1), length(X2)), add = TRUE)
points(grid_set, pch = '.', col = ifelse(y_grid == 1, 'springgreen3', 'tomato'))
points(set, pch = 21, bg = ifelse(set[, 3] == 1, 'green4', 'red3'))
```

![](RandomForestClassification_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
