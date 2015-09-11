# EDA-NYTClicks
Monnie McGee  
September 9, 2015  

This is the R markdown document for keeping track of code used to explore the NYT clicks data from May 1, 2012. 

Required packages: doBy and ggplot2. Install and/or load these packages before trying the code below.

```r
library(ggplot2)
library(doBy)
```

```
## Loading required package: survival
```


```r
# Enter the data
fileLocation <- "http://stat.columbia.edu/~rachel/datasets/nyt1.csv"
data1 <- read.csv(url(fileLocation))
names(data1) # What are the variable names?
```

```
## [1] "Age"         "Gender"      "Impressions" "Clicks"      "Signed_In"
```

```r
str(data1)
```

```
## 'data.frame':	458441 obs. of  5 variables:
##  $ Age        : int  36 73 30 49 47 47 0 46 16 52 ...
##  $ Gender     : int  0 1 0 1 1 0 0 0 0 0 ...
##  $ Impressions: int  3 3 3 3 11 11 7 5 3 4 ...
##  $ Clicks     : int  0 0 0 0 0 1 1 0 0 0 ...
##  $ Signed_In  : int  1 1 1 1 1 1 0 1 1 1 ...
```
The "str" function (pronounced "stir") tells us that all of the variables are in integer format. We may want to change the format of certain variables later, depending on what we are doing with the variable.

## Task 1: Exploratory Data Analysis

```r
# Numerical summary of the data. A good place to start. 
summary(data1)
```

```
##       Age             Gender       Impressions         Clicks       
##  Min.   :  0.00   Min.   :0.000   Min.   : 0.000   Min.   :0.00000  
##  1st Qu.:  0.00   1st Qu.:0.000   1st Qu.: 3.000   1st Qu.:0.00000  
##  Median : 31.00   Median :0.000   Median : 5.000   Median :0.00000  
##  Mean   : 29.48   Mean   :0.367   Mean   : 5.007   Mean   :0.09259  
##  3rd Qu.: 48.00   3rd Qu.:1.000   3rd Qu.: 6.000   3rd Qu.:0.00000  
##  Max.   :108.00   Max.   :1.000   Max.   :20.000   Max.   :4.00000  
##    Signed_In     
##  Min.   :0.0000  
##  1st Qu.:0.0000  
##  Median :1.0000  
##  Mean   :0.7009  
##  3rd Qu.:1.0000  
##  Max.   :1.0000
```
## **Question:** Signed_In and Gender are both indicator variables. What does the mean of each of these variables tell us?


```r
# distribution of ages
hist(data1$Age, main="", xlab="Age")
```

![](EDA-NYTdata_files/figure-html/unnamed-chunk-4-1.png) 

```r
quantile(data1$Age) 
```

```
##   0%  25%  50%  75% 100% 
##    0    0   31   48  108
```

```r
# distribution of Impressions
hist(data1$Impressions, main="", xlab="# of Impressions")
```

![](EDA-NYTdata_files/figure-html/unnamed-chunk-4-2.png) 

```r
quantile(data1$Impressions)
```

```
##   0%  25%  50%  75% 100% 
##    0    3    5    6   20
```

```r
# distribution of Clicks
hist(data1$Clicks, main="", xlab="# of Clicks")
```

![](EDA-NYTdata_files/figure-html/unnamed-chunk-4-3.png) 

```r
quantile(data1$Clicks)
```

```
##   0%  25%  50%  75% 100% 
##    0    0    0    0    4
```

It looks like that Age is the only continuous variable. Clicks and Impressions are probably discrete (what makes me think that?). Let's use the 'table' function to determine the values of Clicks and Impressions and how many cases are assigned to each.


```r
table(data1$Clicks)
```

```
## 
##      0      1      2      3      4 
## 418603  37372   2330    127      9
```

```r
table(data1$Impressions)
```

```
## 
##     0     1     2     3     4     5     6     7     8     9    10    11 
##  3066 15483 38433 64121 80303 80477 66808 48066 30081 16965  8357  3771 
##    12    13    14    15    16    17    18    20 
##  1549   647   211    64    25    11     2     1
```
## **Question:** What do we learn from the table function? 

## **Question:** What does does the function "table(data1$AgeGroup,data1$Clicks)" tell us? 

## Task 2: Create a new variable for age groups that categorizes users as "<18", "18-24", "25-34", "35-44", "45-54", "55-64", and "65+".

```r
# Create age groups
data1$AgeGroup <- cut(data1$Age, c(-Inf, 18, 24, 34, 44, 54, 64, Inf))
levels(data1$AgeGroup) <- c("<18", "18-24", "25-34", "35-44", "45-54", "55-64", "65+")
summaryBy(Gender+Signed_In+Impressions+Clicks~AgeGroup, data=data1)
```

```
##   AgeGroup Gender.mean Signed_In.mean Impressions.mean Clicks.mean
## 1      <18  0.07906215      0.1231277         4.999571  0.14072193
## 2    18-24  0.53385313      1.0000000         5.006635  0.04845478
## 3    25-34  0.53216213      1.0000000         4.993829  0.05048647
## 4    35-44  0.53169630      1.0000000         5.021507  0.05167937
## 5    45-54  0.52897897      1.0000000         5.010406  0.05027377
## 6    55-64  0.53618848      1.0000000         5.022308  0.10183736
## 7      65+  0.36326644      1.0000000         5.012347  0.15128856
```

```r
# Create a function. What does this function do?
siterange <- function(x){c(n=length(x),MIN=min(x),MEAN=mean(x),MAX=max(x))}
summaryBy(Age~AgeGroup, data=data1, FUN=siterange)
```

```
##   AgeGroup  Age.n Age.MIN  Age.MEAN Age.MAX
## 1      <18 156358       0  1.974168      18
## 2    18-24  35270      19 21.269039      24
## 3    25-34  58174      25 29.503352      34
## 4    35-44  70860      35 39.494680      44
## 5    45-54  64288      45 49.492580      54
## 6    55-64  44738      55 59.498189      64
## 7      65+  28753      65 72.988697     108
```

```r
# Histogram of Impressions by Age Group
ggplot(data1,aes(x=Impressions,fill=AgeGroup))+geom_histogram(binwidth=1)
```

![](EDA-NYTdata_files/figure-html/unnamed-chunk-6-1.png) 

```r
# Boxplot of Impressions by Age Group
ggplot(data1,aes(x=AgeGroup,y=Impressions))+geom_boxplot()
```

![](EDA-NYTdata_files/figure-html/unnamed-chunk-6-2.png) 

## **Question:** Why are there individuals that are 0 years old in the data set? Describe these individuals using the other variables. Should we delete these individuals from the data set? Why or why not?  The code below is a good starting place. What else would you want to do (if anything)?


```r
dataChild <- subset(data1, Age==0)
dim(dataChild)
```

```
## [1] 137106      6
```

```r
summary(dataChild)
```

```
##       Age        Gender   Impressions     Clicks         Signed_In
##  Min.   :0   Min.   :0   Min.   : 0   Min.   :0.0000   Min.   :0  
##  1st Qu.:0   1st Qu.:0   1st Qu.: 3   1st Qu.:0.0000   1st Qu.:0  
##  Median :0   Median :0   Median : 5   Median :0.0000   Median :0  
##  Mean   :0   Mean   :0   Mean   : 5   Mean   :0.1421   Mean   :0  
##  3rd Qu.:0   3rd Qu.:0   3rd Qu.: 6   3rd Qu.:0.0000   3rd Qu.:0  
##  Max.   :0   Max.   :0   Max.   :18   Max.   :4.0000   Max.   :0  
##                                                                   
##   AgeGroup     
##  <18  :137106  
##  18-24:     0  
##  25-34:     0  
##  35-44:     0  
##  45-54:     0  
##  55-64:     0  
##  65+  :     0
```

```r
summaryBy(Signed_In+Clicks+Impressions~Gender,data=dataChild,FUN=siterange)
```

```
##   Gender Signed_In.n Signed_In.MIN Signed_In.MEAN Signed_In.MAX Clicks.n
## 1      0      137106             0              0             0   137106
##   Clicks.MIN Clicks.MEAN Clicks.MAX Impressions.n Impressions.MIN
## 1          0   0.1420799          4        137106               0
##   Impressions.MEAN Impressions.MAX
## 1         4.999657              18
```

## **Question:** Does Gender=0 always mean that the user is female? Why or why not?

## Task 3: For a single day, plot distributions of number impressions and click-through-rate (CTR = click/impressions) for these age categories


```r
# Define a new variable to segment Impressions into two groups
data1$hasImps <- cut(data1$Impressions, c(-Inf,0,Inf))
summaryBy(Clicks~hasImps, data=data1, FUN=siterange)
```

```
##    hasImps Clicks.n Clicks.MIN Clicks.MEAN Clicks.MAX
## 1 (-Inf,0]     3066          0  0.00000000          0
## 2 (0, Inf]   455375          0  0.09321768          4
```

```r
# Create a factor based on Impressions and Clicks
data1$scode[data1$Impressions == 0] <- "NoImps"
data1$scode[data1$Impressions > 0] <- "Imps"
data1$scode[data1$Clicks > 0] <- "Clicks"
data1$scode <- factor(data1$scode)
head(data1)
```

```
##   Age Gender Impressions Clicks Signed_In AgeGroup  hasImps  scode
## 1  36      0           3      0         1    35-44 (0, Inf]   Imps
## 2  73      1           3      0         1      65+ (0, Inf]   Imps
## 3  30      0           3      0         1    25-34 (0, Inf]   Imps
## 4  49      1           3      0         1    45-54 (0, Inf]   Imps
## 5  47      1          11      0         1    45-54 (0, Inf]   Imps
## 6  47      0          11      1         1    45-54 (0, Inf] Clicks
```

```r
# Create a function. What does it do?
clen <- function(x){c(length(x))}
etable <- summaryBy(Impressions~scode+Gender+AgeGroup,data=data1,FUN=clen)
etable
```

```
##     scode Gender AgeGroup Impressions.clen
## 1  Clicks      0      <18            18622
## 2  Clicks      0    18-24              779
## 3  Clicks      0    25-34             1361
## 4  Clicks      0    35-44             1675
## 5  Clicks      0    45-54             1494
## 6  Clicks      0    55-64             2006
## 7  Clicks      0      65+             2598
## 8  Clicks      1      <18             1525
## 9  Clicks      1    18-24              890
## 10 Clicks      1    25-34             1509
## 11 Clicks      1    35-44             1917
## 12 Clicks      1    45-54             1645
## 13 Clicks      1    55-64             2331
## 14 Clicks      1      65+             1486
## 15   Imps      0      <18           124402
## 16   Imps      0    18-24            15538
## 17   Imps      0    25-34            25690
## 18   Imps      0    35-44            31290
## 19   Imps      0    45-54            28563
## 20   Imps      0    55-64            18626
## 21   Imps      0      65+            15585
## 22   Imps      1      <18            10754
## 23   Imps      1    18-24            17807
## 24   Imps      1    25-34            29241
## 25   Imps      1    35-44            35512
## 26   Imps      1    45-54            32143
## 27   Imps      1    55-64            21499
## 28   Imps      1      65+             8887
## 29 NoImps      0      <18              972
## 30 NoImps      0    18-24              124
## 31 NoImps      0    25-34              165
## 32 NoImps      0    35-44              219
## 33 NoImps      0    45-54              224
## 34 NoImps      0    55-64              118
## 35 NoImps      0      65+              125
## 36 NoImps      1      <18               83
## 37 NoImps      1    18-24              132
## 38 NoImps      1    25-34              208
## 39 NoImps      1    35-44              247
## 40 NoImps      1    45-54              219
## 41 NoImps      1    55-64              158
## 42 NoImps      1      65+               72
```

## **Question:** What does 'etable' tell us?

## **Question:** What does it mean to have zero Impressions? What does it mean to have zero Impressions and a postive number of clicks?


```r
# Create plot of CTR for a subset of the data where Impressions > 0
d1 <- subset(data1, Impressions > 0)
d1$CTR <- d1$Clicks/d1$Impressions
ggplot(d1,aes(x=CTR, colour=AgeGroup))+geom_density()
```

![](EDA-NYTdata_files/figure-html/unnamed-chunk-9-1.png) 

```r
ggplot(subset(d1, Clicks >0),aes(x=CTR, colour=AgeGroup))+geom_density()
```

![](EDA-NYTdata_files/figure-html/unnamed-chunk-9-2.png) 

```r
ggplot(subset(d1, CTR>0), aes(x=CTR, fill=AgeGroup))+
    labs(title="Click-through rate by age group (05/01/2012)")+
    geom_histogram(binwidth=.025)
```

![](EDA-NYTdata_files/figure-html/unnamed-chunk-9-3.png) 

## **Question:** Summarize these plots. What do they tell you about the relationship between Click through rate and age?

## Task 4
* Define a new variable to segment users based on their click behavior
* Explore the data and make visual comparisons of click behavior across user segments
* Create metrics/measurements/statistics that summarize the data
Note that this is an unordered list. You can do this in any order.

## Case Study I due October 1
* Extend this analysis across days
* Visualize some metrics and distributions over time
* Describe and interpret any patterns you find
* Put it all in an R markdown (or md) document
Note: This will take time. You will need to use Google and StackOverflow and other help. You will not be able to analyze absolutely everything. You have other classes. Pick one or two questions of interest to you (not to me!) and concentrate on those.
