# MultipleLinearRegression



### Regression - Theory
Regression models (both linear and non-linear) are used for predicting a real value, like salary for example. If your independent variable is time, then you are forecasting future values, otherwise your model is predicting present but unknown values. Regression technique vary from Linear Regression to SVR and Random Forests Regression.

Machine Learning Regression models:

* Simple Linear Regression
* Multiple Linear Regression
* Polynomial Regression
* Support Vector for Regression (SVR)
* Decision Tree Classification
* Random Forest Classification

### Multiple Linear Regression - Theory
Multiple linear regression attempts to model the relationship between two or more explanatory variables and a response variable by fitting a linear equation to observed data. Every value of the independent variable x is associated with a value of the dependent variable y.

* Pros: Works on any size of dataset, gives informations about relevance of features
* Cons: The Linear Regression Assumptions (http://www.statisticssolutions.com/assumptions-of-linear-regression/)

### Business Problem (Udemy)
* Investors would like to know in which companies they should invest in.
* Which indicators help to predict if a company is going to be profitable or not

Dataset is a sample of companies, including their expenses and profit

-- Importing and preparing dataset --

```r
dataset = read.csv('50_Startups.csv')
head(dataset)
```

```
##   R.D.Spend Administration Marketing.Spend      State   Profit
## 1  165349.2      136897.80        471784.1   New York 192261.8
## 2  162597.7      151377.59        443898.5 California 191792.1
## 3  153441.5      101145.55        407934.5    Florida 191050.4
## 4  144372.4      118671.85        383199.6   New York 182902.0
## 5  142107.3       91391.77        366168.4    Florida 166187.9
## 6  131876.9       99814.71        362861.4   New York 156991.1
```

```r
# Encoding categorical data
dataset$State = factor(dataset$State,
                         levels = c('New York', 'California', 'Florida'),
                         labels = c(1, 2, 3))
```

-- Splitting the dataset into a Training set and a Test set  -- 

```r
library(caTools)
set.seed(123) # only needed for training purposes to get same results as instructor
split = sample.split(dataset$Profit, SplitRatio = 0.8)
training_set = subset(dataset, split == TRUE) # 40 obs.
test_set = subset(dataset, split == FALSE) # 10 obs.
```

-- Fitting Multiple Linear Regression ot the Training set -- 

```r
regressor = lm(formula = Profit ~ ., data = training_set)
summary(regressor)
```

```
## 
## Call:
## lm(formula = Profit ~ ., data = training_set)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -33128  -4865      5   6098  18065 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      4.965e+04  7.637e+03   6.501 1.94e-07 ***
## R.D.Spend        7.986e-01  5.604e-02  14.251 6.70e-16 ***
## Administration  -2.942e-02  5.828e-02  -0.505    0.617    
## Marketing.Spend  3.268e-02  2.127e-02   1.537    0.134    
## State2           1.213e+02  3.751e+03   0.032    0.974    
## State3           2.376e+02  4.127e+03   0.058    0.954    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 9908 on 34 degrees of freedom
## Multiple R-squared:  0.9499,	Adjusted R-squared:  0.9425 
## F-statistic:   129 on 5 and 34 DF,  p-value: < 2.2e-16
```
According to Significance level, it looks like R.D Spend is the only strong predictor

-- Predicting the test results --

```r
y_pred = predict(regressor, newdata = test_set)
y_pred
```

```
##         4         5         8        11        16        20        21 
## 173981.09 172655.64 160250.02 135513.90 146059.36 114151.03 117081.62 
##        24        31        32 
## 110671.31  98975.29  96867.03
```

```r
test_set[,5]
```

```
##  [1] 182901.99 166187.94 155752.60 146121.95 129917.04 122776.86 118474.03
##  [8] 108733.99  99937.59  97483.56
```

-- Building the optimal model using Backward Elimination -- 
Step 1: Select Significanse level: SL = 0.05
Step 2: Fit the full model with all possible predictors (using full dataset)

```r
regressor = lm(formula = Profit ~ R.D.Spend + Administration + Marketing.Spend + State, data = dataset)
```
Step 3: Consider the predictor with HIGHEST p-value. If P>SL, go to Step 4

```r
summary(regressor)
```

```
## 
## Call:
## lm(formula = Profit ~ R.D.Spend + Administration + Marketing.Spend + 
##     State, data = dataset)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -33504  -4736     90   6672  17338 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      5.008e+04  6.953e+03   7.204 5.76e-09 ***
## R.D.Spend        8.060e-01  4.641e-02  17.369  < 2e-16 ***
## Administration  -2.700e-02  5.223e-02  -0.517    0.608    
## Marketing.Spend  2.698e-02  1.714e-02   1.574    0.123    
## State2           4.189e+01  3.256e+03   0.013    0.990    
## State3           2.407e+02  3.339e+03   0.072    0.943    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 9439 on 44 degrees of freedom
## Multiple R-squared:  0.9508,	Adjusted R-squared:  0.9452 
## F-statistic: 169.9 on 5 and 44 DF,  p-value: < 2.2e-16
```
Highest p-value is 0.990 > 0.05--> State2
2nd Highest p-value is 0.943 > 0.05 --> State 3

Step 4: Remove predictor

```r
regressor = lm(formula = Profit ~ R.D.Spend + Administration + Marketing.Spend, data = dataset)
summary(regressor)
```

```
## 
## Call:
## lm(formula = Profit ~ R.D.Spend + Administration + Marketing.Spend, 
##     data = dataset)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -33534  -4795     63   6606  17275 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      5.012e+04  6.572e+03   7.626 1.06e-09 ***
## R.D.Spend        8.057e-01  4.515e-02  17.846  < 2e-16 ***
## Administration  -2.682e-02  5.103e-02  -0.526    0.602    
## Marketing.Spend  2.723e-02  1.645e-02   1.655    0.105    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 9232 on 46 degrees of freedom
## Multiple R-squared:  0.9507,	Adjusted R-squared:  0.9475 
## F-statistic:   296 on 3 and 46 DF,  p-value: < 2.2e-16
```
Highest p-value is 0.602 > 0.05 --> remove Administration 


```r
regressor = lm(formula = Profit ~ R.D.Spend + Marketing.Spend, data = dataset)
summary(regressor)
```

```
## 
## Call:
## lm(formula = Profit ~ R.D.Spend + Marketing.Spend, data = dataset)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -33645  -4632   -414   6484  17097 
## 
## Coefficients:
##                  Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     4.698e+04  2.690e+03  17.464   <2e-16 ***
## R.D.Spend       7.966e-01  4.135e-02  19.266   <2e-16 ***
## Marketing.Spend 2.991e-02  1.552e-02   1.927     0.06 .  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 9161 on 47 degrees of freedom
## Multiple R-squared:  0.9505,	Adjusted R-squared:  0.9483 
## F-statistic: 450.8 on 2 and 47 DF,  p-value: < 2.2e-16
```
Highest p-value is 0.6 > 0.05 --> Marketing.Spend 
Attention: very close to 5%!


```r
regressor = lm(formula = Profit ~ R.D.Spend, data = dataset)
summary(regressor)
```

```
## 
## Call:
## lm(formula = Profit ~ R.D.Spend, data = dataset)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -34351  -4626   -375   6249  17188 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 4.903e+04  2.538e+03   19.32   <2e-16 ***
## R.D.Spend   8.543e-01  2.931e-02   29.15   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 9416 on 48 degrees of freedom
## Multiple R-squared:  0.9465,	Adjusted R-squared:  0.9454 
## F-statistic: 849.8 on 1 and 48 DF,  p-value: < 2.2e-16
```
Highest p-value is <2e-16 < 0.05 --> FIN

================================================================

## Business problem 2 

Which attributes influence the quality of white wine?

Wine quality white. https://archive.ics.uci.edu/ml/machine-learning-databases/wine-quality/winequality-white.cs

Dataset Description: https://archive.ics.uci.edu/ml/machine-learning-databases/wine-quality/winequality.names

Input variables (based on physicochemical tests):
1 - fixed acidity
2 - volatile acidity
3 - citric acid
4 - residual sugar
5 - chlorides
6 - free sulfur dioxide
7 - total sulfur dioxide
8 - density
9 - pH
10 - sulphates
11 - alcohol
   
Output variable (based on sensory data): 
12 - quality (score between 0 and 10)
   

```r
set.seed(10)
url <- "https://archive.ics.uci.edu/ml/machine-learning-databases/wine-quality/winequality-white.csv"
download.file(url, "whitewine.csv")
whitewine <- read.csv("whitewine.csv", sep = ";")
summary(whitewine)
```

```
##  fixed.acidity    volatile.acidity  citric.acid     residual.sugar  
##  Min.   : 3.800   Min.   :0.0800   Min.   :0.0000   Min.   : 0.600  
##  1st Qu.: 6.300   1st Qu.:0.2100   1st Qu.:0.2700   1st Qu.: 1.700  
##  Median : 6.800   Median :0.2600   Median :0.3200   Median : 5.200  
##  Mean   : 6.855   Mean   :0.2782   Mean   :0.3342   Mean   : 6.391  
##  3rd Qu.: 7.300   3rd Qu.:0.3200   3rd Qu.:0.3900   3rd Qu.: 9.900  
##  Max.   :14.200   Max.   :1.1000   Max.   :1.6600   Max.   :65.800  
##    chlorides       free.sulfur.dioxide total.sulfur.dioxide
##  Min.   :0.00900   Min.   :  2.00      Min.   :  9.0       
##  1st Qu.:0.03600   1st Qu.: 23.00      1st Qu.:108.0       
##  Median :0.04300   Median : 34.00      Median :134.0       
##  Mean   :0.04577   Mean   : 35.31      Mean   :138.4       
##  3rd Qu.:0.05000   3rd Qu.: 46.00      3rd Qu.:167.0       
##  Max.   :0.34600   Max.   :289.00      Max.   :440.0       
##     density             pH          sulphates         alcohol     
##  Min.   :0.9871   Min.   :2.720   Min.   :0.2200   Min.   : 8.00  
##  1st Qu.:0.9917   1st Qu.:3.090   1st Qu.:0.4100   1st Qu.: 9.50  
##  Median :0.9937   Median :3.180   Median :0.4700   Median :10.40  
##  Mean   :0.9940   Mean   :3.188   Mean   :0.4898   Mean   :10.51  
##  3rd Qu.:0.9961   3rd Qu.:3.280   3rd Qu.:0.5500   3rd Qu.:11.40  
##  Max.   :1.0390   Max.   :3.820   Max.   :1.0800   Max.   :14.20  
##     quality     
##  Min.   :3.000  
##  1st Qu.:5.000  
##  Median :6.000  
##  Mean   :5.878  
##  3rd Qu.:6.000  
##  Max.   :9.000
```
   
Backward Elimination process
1. Select Significance Level (SL) SL = 0.05
2. Fit full model with all predictors 
3. As long as P > SL remove P with highest P-value
4. Fit model without this variable


```r
regressor = lm(formula = quality ~ ., data = whitewine)
summary(regressor)
```

```
## 
## Call:
## lm(formula = quality ~ ., data = whitewine)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.8348 -0.4934 -0.0379  0.4637  3.1143 
## 
## Coefficients:
##                        Estimate Std. Error t value Pr(>|t|)    
## (Intercept)           1.502e+02  1.880e+01   7.987 1.71e-15 ***
## fixed.acidity         6.552e-02  2.087e-02   3.139  0.00171 ** 
## volatile.acidity     -1.863e+00  1.138e-01 -16.373  < 2e-16 ***
## citric.acid           2.209e-02  9.577e-02   0.231  0.81759    
## residual.sugar        8.148e-02  7.527e-03  10.825  < 2e-16 ***
## chlorides            -2.473e-01  5.465e-01  -0.452  0.65097    
## free.sulfur.dioxide   3.733e-03  8.441e-04   4.422 9.99e-06 ***
## total.sulfur.dioxide -2.857e-04  3.781e-04  -0.756  0.44979    
## density              -1.503e+02  1.907e+01  -7.879 4.04e-15 ***
## pH                    6.863e-01  1.054e-01   6.513 8.10e-11 ***
## sulphates             6.315e-01  1.004e-01   6.291 3.44e-10 ***
## alcohol               1.935e-01  2.422e-02   7.988 1.70e-15 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.7514 on 4886 degrees of freedom
## Multiple R-squared:  0.2819,	Adjusted R-squared:  0.2803 
## F-statistic: 174.3 on 11 and 4886 DF,  p-value: < 2.2e-16
```
Highest p-value is 0.81759 > 0.05--> citric.acid

Remove predictor

```r
regressor = lm(formula = quality ~ fixed.acidity + volatile.acidity + residual.sugar + chlorides + free.sulfur.dioxide + total.sulfur.dioxide + density + pH + sulphates + alcohol, data = whitewine)
summary(regressor)
```

```
## 
## Call:
## lm(formula = quality ~ fixed.acidity + volatile.acidity + residual.sugar + 
##     chlorides + free.sulfur.dioxide + total.sulfur.dioxide + 
##     density + pH + sulphates + alcohol, data = whitewine)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.8388 -0.4907 -0.0370  0.4665  3.1153 
## 
## Coefficients:
##                        Estimate Std. Error t value Pr(>|t|)    
## (Intercept)           1.499e+02  1.876e+01   7.991 1.66e-15 ***
## fixed.acidity         6.611e-02  2.071e-02   3.192  0.00142 ** 
## volatile.acidity     -1.868e+00  1.121e-01 -16.668  < 2e-16 ***
## residual.sugar        8.140e-02  7.519e-03  10.827  < 2e-16 ***
## chlorides            -2.338e-01  5.434e-01  -0.430  0.66701    
## free.sulfur.dioxide   3.740e-03  8.435e-04   4.434 9.46e-06 ***
## total.sulfur.dioxide -2.822e-04  3.777e-04  -0.747  0.45501    
## density              -1.500e+02  1.903e+01  -7.882 3.94e-15 ***
## pH                    6.843e-01  1.050e-01   6.517 7.90e-11 ***
## sulphates             6.324e-01  1.003e-01   6.306 3.12e-10 ***
## alcohol               1.940e-01  2.411e-02   8.046 1.06e-15 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.7513 on 4887 degrees of freedom
## Multiple R-squared:  0.2819,	Adjusted R-squared:  0.2804 
## F-statistic: 191.8 on 10 and 4887 DF,  p-value: < 2.2e-16
```
Highest p-value is 0.66701 > 0.05--> chlorides

Remove predictor

```r
regressor = lm(formula = quality ~ fixed.acidity + volatile.acidity + residual.sugar + free.sulfur.dioxide + total.sulfur.dioxide + density + pH + sulphates + alcohol, data = whitewine)
summary(regressor)
```

```
## 
## Call:
## lm(formula = quality ~ fixed.acidity + volatile.acidity + residual.sugar + 
##     free.sulfur.dioxide + total.sulfur.dioxide + density + pH + 
##     sulphates + alcohol, data = whitewine)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.8355 -0.4901 -0.0391  0.4675  3.1158 
## 
## Coefficients:
##                        Estimate Std. Error t value Pr(>|t|)    
## (Intercept)           1.513e+02  1.849e+01   8.179 3.60e-16 ***
## fixed.acidity         6.754e-02  2.045e-02   3.303 0.000963 ***
## volatile.acidity     -1.872e+00  1.117e-01 -16.761  < 2e-16 ***
## residual.sugar        8.206e-02  7.361e-03  11.148  < 2e-16 ***
## free.sulfur.dioxide   3.728e-03  8.430e-04   4.422 9.98e-06 ***
## total.sulfur.dioxide -2.845e-04  3.776e-04  -0.753 0.451306    
## density              -1.514e+02  1.874e+01  -8.078 8.24e-16 ***
## pH                    6.922e-01  1.034e-01   6.695 2.40e-11 ***
## sulphates             6.339e-01  1.002e-01   6.324 2.77e-10 ***
## alcohol               1.940e-01  2.411e-02   8.046 1.06e-15 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.7512 on 4888 degrees of freedom
## Multiple R-squared:  0.2818,	Adjusted R-squared:  0.2805 
## F-statistic: 213.1 on 9 and 4888 DF,  p-value: < 2.2e-16
```
Highest p-value is 0.451306 > 0.05--> total.sulfur.dioxide

Remove predictor

```r
regressor = lm(formula = quality ~ fixed.acidity + volatile.acidity + residual.sugar + free.sulfur.dioxide + density + pH + sulphates + alcohol, data = whitewine)
summary(regressor)
```

```
## 
## Call:
## lm(formula = quality ~ fixed.acidity + volatile.acidity + residual.sugar + 
##     free.sulfur.dioxide + density + pH + sulphates + alcohol, 
##     data = whitewine)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.8246 -0.4938 -0.0396  0.4660  3.1208 
## 
## Coefficients:
##                       Estimate Std. Error t value Pr(>|t|)    
## (Intercept)          1.541e+02  1.810e+01   8.514  < 2e-16 ***
## fixed.acidity        6.810e-02  2.043e-02   3.333 0.000864 ***
## volatile.acidity    -1.888e+00  1.095e-01 -17.242  < 2e-16 ***
## residual.sugar       8.285e-02  7.287e-03  11.370  < 2e-16 ***
## free.sulfur.dioxide  3.349e-03  6.766e-04   4.950 7.67e-07 ***
## density             -1.543e+02  1.834e+01  -8.411  < 2e-16 ***
## pH                   6.942e-01  1.034e-01   6.717 2.07e-11 ***
## sulphates            6.285e-01  9.997e-02   6.287 3.52e-10 ***
## alcohol              1.932e-01  2.408e-02   8.021 1.31e-15 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.7512 on 4889 degrees of freedom
## Multiple R-squared:  0.2818,	Adjusted R-squared:  0.2806 
## F-statistic: 239.7 on 8 and 4889 DF,  p-value: < 2.2e-16
```

Training model

```r
split = sample.split(whitewine$quality, SplitRatio = 0.8)
training_set = subset(whitewine, split == TRUE) # 3918 obs.
test_set = subset(whitewine, split == FALSE) # 980 obs.
```

-- Fitting Multiple Linear Regression ot the Training set -- 

```r
regressor = lm(formula = quality ~ fixed.acidity + volatile.acidity + residual.sugar + free.sulfur.dioxide + density + pH + sulphates + alcohol, data = whitewine)
```

-- Predicting the test results --

```r
y_pred = predict(regressor, newdata = test_set)
```
Prediction (first 20 winesamples):

```r
round(y_pred[1:20])
```

```
##  15  16  18  24  33  34  40  45  53  58  61  62  66  72  73  91  94  97 
##   6   6   6   4   6   6   6   6   6   6   5   6   5   5   6   5   7   6 
## 114 131 
##   5   6
```
Actual quality measures (first 20 winesamples)

```r
test_set[1:20,12]
```

```
##  [1] 5 7 8 5 6 6 5 6 7 6 6 6 5 5 5 6 7 6 5 5
```
