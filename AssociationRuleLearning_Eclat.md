# Association Rule Learning - Eclat



### Association Rule Learning - Theory
Mines frequent itemsets, association rules or association hyperedges.
People who bought also bought ... 

Association Rule Learning models:

* Apriori
* Eclat

### Eclat - Theory


Step 1:Set a minimum support
Step 2: Take all the subsets in transactions having higher support than nimimum support
Step 3: Sort the rules by decreasing support

### Business Problem (Udemy)
Which products are likely to be bought together? 
Dataset contains products that were bought by each customer during an entire week

-- Data Preprocessing -- 

```r
dataset <- read.csv('Market_Basket_Optimisation.csv', sep = ',', header = FALSE)
head(dataset)
```

```
##               V1        V2         V3               V4           V5
## 1         shrimp   almonds    avocado   vegetables mix green grapes
## 2        burgers meatballs       eggs                              
## 3        chutney                                                   
## 4         turkey   avocado                                         
## 5  mineral water      milk energy bar whole wheat rice    green tea
## 6 low fat yogurt                                                   
##                 V6   V7             V8           V9          V10
## 1 whole weat flour yams cottage cheese energy drink tomato juice
## 2                                                               
## 3                                                               
## 4                                                               
## 5                                                               
## 6                                                               
##              V11       V12   V13   V14           V15    V16
## 1 low fat yogurt green tea honey salad mineral water salmon
## 2                                                          
## 3                                                          
## 4                                                          
## 5                                                          
## 6                                                          
##                 V17             V18     V19       V20
## 1 antioxydant juice frozen smoothie spinach olive oil
## 2                                                    
## 3                                                    
## 4                                                    
## 5                                                    
## 6
```

Create sparse matrix:

```r
library(arules)
# if there are duplicates in the dataset (eg same product twice) remove them with rm.duplicates = TRUE
dataset <- read.transactions('Market_Basket_Optimisation.csv', sep = ',', rm.duplicates = TRUE)
```

```
## distribution of transactions with duplicates:
## 1 
## 5
```

shows there are 5 transactions with duplicates
1 (= duplicates, 2 would be triplates)


```r
summary(dataset)
```

```
## transactions as itemMatrix in sparse format with
##  7501 rows (elements/itemsets/transactions) and
##  119 columns (items) and a density of 0.03288973 
## 
## most frequent items:
## mineral water          eggs     spaghetti  french fries     chocolate 
##          1788          1348          1306          1282          1229 
##       (Other) 
##         22405 
## 
## element (itemset/transaction) length distribution:
## sizes
##    1    2    3    4    5    6    7    8    9   10   11   12   13   14   15 
## 1754 1358 1044  816  667  493  391  324  259  139  102   67   40   22   17 
##   16   18   19   20 
##    4    1    2    1 
## 
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   1.000   2.000   3.000   3.914   5.000  20.000 
## 
## includes extended item information - examples:
##              labels
## 1           almonds
## 2 antioxydant juice
## 3         asparagus
```

We have 3% non-zero values (density of 0.03288973 )
1754 baskets containing one product
smallest basket head 1 item, largest 20 items, on average 4


Show 100 most purchased products:

```r
itemFrequencyPlot(dataset, topN = 100) # topN = most frequent purchased products
```

![](AssociationRuleLearning_Eclat_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

Show 10 most purchased products:

```r
itemFrequencyPlot(dataset, topN = 10)
```

![](AssociationRuleLearning_Eclat_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

-- Training Eclat on the dataset -- 

```r
rules = eclat(dataset, parameter = list(support = 0.004, minlen = 2)

# will contain different rules of our business problem

# support: 
# many products are not purchased often --> they have small support and thus are not relevant to us.
# we set minimimum support by looking at products that are purchased rather frequently, eg at least three or four times a day
# 3times a day -> 21 times a week / 7500 purchases a week = 0.0028 --> rounded to 0.003

# confidence: 
# depends on business goals. 
# Too small confidence will lead to nonsense rules (chocolate + shampoo)
# Default: 0.8
)
```

```
## Eclat
## 
## parameter specification:
##  tidLists support minlen maxlen            target   ext
##     FALSE   0.004      2     10 frequent itemsets FALSE
## 
## algorithmic control:
##  sparse sort verbose
##       7   -2    TRUE
## 
## Absolute minimum support count: 30 
## 
## create itemset ... 
## set transactions ...[119 item(s), 7501 transaction(s)] done [0.00s].
## sorting and recoding items ... [114 item(s)] done [0.00s].
## creating sparse bit matrix ... [114 row(s), 7501 column(s)] done [0.00s].
## writing  ... [845 set(s)] done [0.02s].
## Creating S4 object  ... done [0.00s].
```


-- Visualising the results -- 

```r
# shows 10 strongest rules, sorted by support
inspect(sort(rules, by = 'support')[1:10])
```

```
##      items                             support   
## [1]  {mineral water,spaghetti}         0.05972537
## [2]  {chocolate,mineral water}         0.05265965
## [3]  {eggs,mineral water}              0.05092654
## [4]  {milk,mineral water}              0.04799360
## [5]  {ground beef,mineral water}       0.04092788
## [6]  {ground beef,spaghetti}           0.03919477
## [7]  {chocolate,spaghetti}             0.03919477
## [8]  {eggs,spaghetti}                  0.03652846
## [9]  {eggs,french fries}               0.03639515
## [10] {frozen vegetables,mineral water} 0.03572857
```

Different sets of items most frequently bought together
