# Association Rule Learning



### Association Rule Learning - Theory
Mines frequent itemsets, association rules or association hyperedges.
People who bought also bought ... 

Association Rule Learning models:

* Apriori
* Eclat

### Apriori - Theory
The Apriori algorithm employs level-wise search for frequent itemsets. The implementation of Apriori used includes some improvements (e.g., a prefix tree and item sorting).

Step 1:Set a minimum support and confidence
Step 2: Take all the subsets in transactions having higher support than nimimum support
Step 3: Take all the rules of these subsets having higher confidence than minimum confidence
Step 4: Sort the rules by decreasing lift

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


```r
# create sparse matrix
# if there are duplicates in the dataset (eg same product twice) remove them with rm.duplicates = TRUE
library(arules)
```

```
## Loading required package: Matrix
```

```
## 
## Attaching package: 'arules'
```

```
## The following objects are masked from 'package:base':
## 
##     abbreviate, write
```

```r
dataset <- read.transactions('Market_Basket_Optimisation.csv', sep = ',', rm.duplicates = TRUE)
```

```
## distribution of transactions with duplicates:
## 1 
## 5
```

```r
head(dataset)
```

```
## transactions in sparse format with
##  6 transactions (rows) and
##  119 items (columns)
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

Show most purchased product:

```r
itemFrequencyPlot(dataset, topN = 100)
```

![](AssociationRuleLearning_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
# topN = most frequent purchased products
```


```r
itemFrequencyPlot(dataset, topN = 10)
```

![](AssociationRuleLearning_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

-- Training Apriori on the dataset -- 

```r
rules = apriori(dataset, parameter = list(support = 0.003, confidence = 0.8))
```

```
## Apriori
## 
## Parameter specification:
##  confidence minval smax arem  aval originalSupport maxtime support minlen
##         0.8    0.1    1 none FALSE            TRUE       5   0.003      1
##  maxlen target   ext
##      10  rules FALSE
## 
## Algorithmic control:
##  filter tree heap memopt load sort verbose
##     0.1 TRUE TRUE  FALSE TRUE    2    TRUE
## 
## Absolute minimum support count: 22 
## 
## set item appearances ...[0 item(s)] done [0.00s].
## set transactions ...[119 item(s), 7501 transaction(s)] done [0.00s].
## sorting and recoding items ... [115 item(s)] done [0.00s].
## creating transaction tree ... done [0.00s].
## checking subsets of size 1 2 3 4 5 done [0.00s].
## writing ... [0 rule(s)] done [0.00s].
## creating S4 object  ... done [0.00s].
```

```r
# will contain different rules of our business problem

# support: 
# many products are not purchased often --> they have small support and thus are not relevant to us.
# we set minimimum support by looking at products that are purchased rather frequently, eg at least three or four times a day
# 3times a day -> 21 times a week / 7500 purchases a week = 0.0028 --> rounded to 0.003

# confidence: 
# depends on business goals. 
# Too small confidence will lead to nonsense rules (chocolate + shampoo)
# Default: 0.8
```

writing...0 rules because confidence set to 0.8. = each rule should be correct on min 80% of all transactions. This is quite high.
We need to set a smaller confidence. we try with 0.4


```r
rules = apriori(dataset, parameter = list(support = 0.003, confidence = 0.4))
```

```
## Apriori
## 
## Parameter specification:
##  confidence minval smax arem  aval originalSupport maxtime support minlen
##         0.4    0.1    1 none FALSE            TRUE       5   0.003      1
##  maxlen target   ext
##      10  rules FALSE
## 
## Algorithmic control:
##  filter tree heap memopt load sort verbose
##     0.1 TRUE TRUE  FALSE TRUE    2    TRUE
## 
## Absolute minimum support count: 22 
## 
## set item appearances ...[0 item(s)] done [0.00s].
## set transactions ...[119 item(s), 7501 transaction(s)] done [0.00s].
## sorting and recoding items ... [115 item(s)] done [0.00s].
## creating transaction tree ... done [0.00s].
## checking subsets of size 1 2 3 4 5 done [0.00s].
## writing ... [281 rule(s)] done [0.00s].
## creating S4 object  ... done [0.00s].
```

-- Visualising the results -- 

```r
# shows 10 strongest rules, sorted by lift
inspect(sort(rules, by = 'lift')[1:10])
```

```
##      lhs                    rhs                     support confidence     lift
## [1]  {mineral water,                                                           
##       whole wheat pasta} => {olive oil}         0.003866151  0.4027778 6.115863
## [2]  {spaghetti,                                                               
##       tomato sauce}      => {ground beef}       0.003066258  0.4893617 4.980600
## [3]  {french fries,                                                            
##       herb & pepper}     => {ground beef}       0.003199573  0.4615385 4.697422
## [4]  {cereals,                                                                 
##       spaghetti}         => {ground beef}       0.003066258  0.4600000 4.681764
## [5]  {frozen vegetables,                                                       
##       mineral water,                                                           
##       soup}              => {milk}              0.003066258  0.6052632 4.670863
## [6]  {chocolate,                                                               
##       herb & pepper}     => {ground beef}       0.003999467  0.4411765 4.490183
## [7]  {chocolate,                                                               
##       mineral water,                                                           
##       shrimp}            => {frozen vegetables} 0.003199573  0.4210526 4.417225
## [8]  {frozen vegetables,                                                       
##       mineral water,                                                           
##       olive oil}         => {milk}              0.003332889  0.5102041 3.937285
## [9]  {cereals,                                                                 
##       ground beef}       => {spaghetti}         0.003066258  0.6764706 3.885303
## [10] {frozen vegetables,                                                       
##       soup}              => {milk}              0.003999467  0.5000000 3.858539
```

example 1: if customer bought mineral water and whole meat pasta they will also buy olive oil in 40 % of the cases
example 3: if people buy french fries and herb&pepper in 46% of the cases they also buy ground beef

Attention: some products are present in a set of items not b/c of good association but because of high support
example 6: chocolate and herb&pepper --> ground beef in 44%. Chocolate has  high support = is bought often (#5)
That doesnt mean that chocolate leads to purchase of ground beef

Choloate and Mineral have high support. Doesnt mean we need to change the support
We might need to change the confidence (currently 40%+). 
Rules have high confidence because ruels are associated to the baskets that contain most purchased products over all.
Highly purchased items go in the same basket without having an association.
--> reduce confidence to 20%


```r
rules = apriori(dataset, parameter = list(support = 0.003, confidence = 0.2))
```

```
## Apriori
## 
## Parameter specification:
##  confidence minval smax arem  aval originalSupport maxtime support minlen
##         0.2    0.1    1 none FALSE            TRUE       5   0.003      1
##  maxlen target   ext
##      10  rules FALSE
## 
## Algorithmic control:
##  filter tree heap memopt load sort verbose
##     0.1 TRUE TRUE  FALSE TRUE    2    TRUE
## 
## Absolute minimum support count: 22 
## 
## set item appearances ...[0 item(s)] done [0.00s].
## set transactions ...[119 item(s), 7501 transaction(s)] done [0.00s].
## sorting and recoding items ... [115 item(s)] done [0.00s].
## creating transaction tree ... done [0.00s].
## checking subsets of size 1 2 3 4 5 done [0.00s].
## writing ... [1348 rule(s)] done [0.00s].
## creating S4 object  ... done [0.00s].
```

Since we reduced confidence level, the algorithm found a lot more rules: 1348


```r
inspect(sort(rules, by = 'lift')[1:10])
```

```
##      lhs                                       rhs             support    
## [1]  {mineral water,whole wheat pasta}      => {olive oil}     0.003866151
## [2]  {frozen vegetables,milk,mineral water} => {soup}          0.003066258
## [3]  {fromage blanc}                        => {honey}         0.003332889
## [4]  {spaghetti,tomato sauce}               => {ground beef}   0.003066258
## [5]  {light cream}                          => {chicken}       0.004532729
## [6]  {pasta}                                => {escalope}      0.005865885
## [7]  {french fries,herb & pepper}           => {ground beef}   0.003199573
## [8]  {cereals,spaghetti}                    => {ground beef}   0.003066258
## [9]  {frozen vegetables,mineral water,soup} => {milk}          0.003066258
## [10] {french fries,ground beef}             => {herb & pepper} 0.003199573
##      confidence lift    
## [1]  0.4027778  6.115863
## [2]  0.2771084  5.484407
## [3]  0.2450980  5.164271
## [4]  0.4893617  4.980600
## [5]  0.2905983  4.843951
## [6]  0.3728814  4.700812
## [7]  0.4615385  4.697422
## [8]  0.4600000  4.681764
## [9]  0.6052632  4.670863
## [10] 0.2307692  4.665768
```
[1] --> purchase of mineral water and pasta leads to purchase of olive oil in 40% of cases --> makes sense
[3] --> very french: white cheese and honey
[4] --> spaghetti + tomato sauce + ground beef --> spaghetti bolognese
[5] --> chicken + light cream --> not so obvious 

Support for products bought at least 4 times a week: 4*7/7500 = 0.0037333 --> 0.004


```r
rules = apriori(dataset, parameter = list(support = 0.004, confidence = 0.2))
```

```
## Apriori
## 
## Parameter specification:
##  confidence minval smax arem  aval originalSupport maxtime support minlen
##         0.2    0.1    1 none FALSE            TRUE       5   0.004      1
##  maxlen target   ext
##      10  rules FALSE
## 
## Algorithmic control:
##  filter tree heap memopt load sort verbose
##     0.1 TRUE TRUE  FALSE TRUE    2    TRUE
## 
## Absolute minimum support count: 30 
## 
## set item appearances ...[0 item(s)] done [0.00s].
## set transactions ...[119 item(s), 7501 transaction(s)] done [0.00s].
## sorting and recoding items ... [114 item(s)] done [0.00s].
## creating transaction tree ... done [0.00s].
## checking subsets of size 1 2 3 4 done [0.00s].
## writing ... [811 rule(s)] done [0.00s].
## creating S4 object  ... done [0.00s].
```

Increased support --> less rules: 811 rules


```r
inspect(sort(rules, by = 'lift')[1:10])
```

```
##      lhs                       rhs                 support confidence     lift
## [1]  {light cream}          => {chicken}       0.004532729  0.2905983 4.843951
## [2]  {pasta}                => {escalope}      0.005865885  0.3728814 4.700812
## [3]  {pasta}                => {shrimp}        0.005065991  0.3220339 4.506672
## [4]  {eggs,                                                                   
##       ground beef}          => {herb & pepper} 0.004132782  0.2066667 4.178455
## [5]  {whole wheat pasta}    => {olive oil}     0.007998933  0.2714932 4.122410
## [6]  {herb & pepper,                                                          
##       spaghetti}            => {ground beef}   0.006399147  0.3934426 4.004360
## [7]  {herb & pepper,                                                          
##       mineral water}        => {ground beef}   0.006665778  0.3906250 3.975683
## [8]  {tomato sauce}         => {ground beef}   0.005332622  0.3773585 3.840659
## [9]  {mushroom cream sauce} => {escalope}      0.005732569  0.3006993 3.790833
## [10] {frozen vegetables,                                                      
##       mineral water,                                                          
##       spaghetti}            => {ground beef}   0.004399413  0.3666667 3.731841
```

By increasing support and excluding products that have support between 0.003 and 0.004 there are new rules: eg. pasta will lead to purchase of shrimp
chicken and cream became number 1

Store manager should try to observe these rules and check changes in revenue after rearranging some of the products, placing them next to each other.
