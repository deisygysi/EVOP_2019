---
title: "Probability and Inference using R."
author: "Deisy Morselli Gysi"
date: "20 March 2019"
---

## What is statistics?
*Statistics* is the part of the scientific method that aims to collect, reduce, analyse and model real data. Statistics helps the analyse and presentation of data, and is also involved with the design of experiments, planning and defining how to collect the data in order to achieve results minimizing errors.

In the end, the statistical analysis should be able to tell us something concrete about the sample that we are studying and allow us to make inferences towards a population.

![](Material_BD_files/figure-html/unnamed-chunk-1-1.png)<!-- -->

Statistics is normally divided into two main methods:
*Descriptive statistics* and *Inferential statistics*.

The descriptive analysis gives an *overview* about the sample and the inferential analysis tests hypothesis about the population.

Before we can understand **what** the data is revealing us, we have to be able to split the variables in its correct category. 

## Types of variables
The variables can be classified into two main groups:

1. *Qualitative* or *Categorical*
  i. *Nominal* (There are no order for the labels)
    + Eg. Country, Gender, Brand of preference, Sides of a coin.
  ii. *Ordinal* (There are an order on the labels)
    + Eg. Degree, State of a disease.

2. *Quantitative* or *Numerical*
  i. *Discrete* (Countable)
    + Eg. The number of kids, Number of births/deaths.
  ii. *Continuous*
    + Eg. Temperature, Age.

In this course, we will use as an example, the data from GSE$5388$, a data set containing data from patients with Bipolar Disorder. In our dataset we have some phenotypic data, and the log transform of the gene expression of some genes.


```r
data = read.csv("Course_2019/Pheno_DE.csv", header = T, row.names = 1)

data[1:5,1:10]
```

<div class="kable-table">

X            Age_death   Age_onset   Alcohol_abuse   Brain_ph  Status              Drug_abuse   Duration_illness  Therapy_Electroconvulsive    Therapy_Fluphenazine
----------  ----------  ----------  --------------  ---------  -----------------  -----------  -----------------  --------------------------  ---------------------
GSM123182           41          21               4       6.60  Bipolar disorder            NA                 20  No                                           4000
GSM123183           51          23               3       6.67  Bipolar disorder            NA                 28  No                                           1200
GSM123184           29          17               5       6.70  Bipolar disorder            NA                 12  No                                              0
GSM123185           45          16               5       6.03  Bipolar disorder            NA                 29  No                                          20000
GSM123186           45          35              NA       6.35  Bipolar disorder            NA                 10  No                                          10000

</div>

```r
names(data)
```

```
##  [1] "X"                         "Age_death"                
##  [3] "Age_onset"                 "Alcohol_abuse"            
##  [5] "Brain_ph"                  "Status"                   
##  [7] "Drug_abuse"                "Duration_illness"         
##  [9] "Therapy_Electroconvulsive" "Therapy_Fluphenazine"     
## [11] "Gender"                    "Therapy_Lithium"          
## [13] "Post_morten_interval"      "Side_brain"               
## [15] "Suicide"                   "Therapy_Valproate"        
## [17] "APOLD1"                    "CLDN10"                   
## [19] "DUSP4"                     "EFEMP1"                   
## [21] "ETNPPL"                    "GJA1"                     
## [23] "PLSCR4"                    "SDC4"                     
## [25] "SLC14A1"                   "SOX9"                     
## [27] "SST"                       "TAC1"                     
## [29] "CX3CR1"                    "DDX3Y"                    
## [31] "ETNPPL.1"                  "G3BP2"                    
## [33] "GABRG2"                    "KDM5D"                    
## [35] "MAFB"                      "NBEA"                     
## [37] "OXR1"                      "PAK1"                     
## [39] "PCDH8"                     "PPID"                     
## [41] "PVALB"                     "RPS4Y1"                   
## [43] "SST.1"                     "TAC1.1"                   
## [45] "TBL1XR1"                   "USP9Y"                    
## [47] "XIST"
```

### Exercise
1. Define the type of each variable in our data set. 

## Descriptive Analysis
### Frequency Tables
Frequency tables can be used for all kinds of variables, however, it's preferable to be used for the *categorical*. However, if you use it for numerical data, the values of the variable have to be divided into *ranges*.

To get an absolute frequency table using R, 
the easiest way is by using the function `table()`, 
and for a relative frequency table `prop.table()`.


```r
table(data$Gender) # Creates an absolute frequency table of Gender
```

```
## 
## Female   Male 
##     21     40
```

```r
table(data$Status)
```

```
## 
## Bipolar disorder  Healthy control 
##               30               31
```

```r
require(magrittr) # Allows using pipe inside R
```

```
## Loading required package: magrittr
```

```r
table(data$Gender) %>% # Pipes the absolute freq. table into
  prop.table()         # a relative freq. table
```

```
## 
##    Female      Male 
## 0.3442623 0.6557377
```

What happens if we ask for a `prop.table()` on an object that is not a table?


```r
 # prop.table(data$Gender)
```

The `table()` function also allows us to create cross-tables. For that, the input is two variables.

```r
table(data$Gender, data$Status)
```

```
##         
##          Bipolar disorder Healthy control
##   Female               14               7
##   Male                 16              24
```

```r
table(data$Gender, data$Status) %>% 
  prop.table(.) %>% 
  round(.,2)
```

```
##         
##          Bipolar disorder Healthy control
##   Female             0.23            0.11
##   Male               0.26            0.39
```

Normally `barplot()` is used to represent visually a frequency table.


```r
table(data$Gender, data$Status) %>% 
  barplot(., beside = TRUE, 
          legend.text = TRUE, 
          col = c('salmon','turquoise'), 
          args.legend = list(x = 'topleft',
                             bty = 'n'),
          ylim = c(0,25),
          las = 1)
```

![](Material_BD_files/figure-html/unnamed-chunk-6-1.png)<!-- -->


### Central tendency measures
The **central tendency measures** attempts to summarise/describe the dataset by its central position. 
The *mean*, *median* and *mode* are examples of central measurements.

The **five-number summary** is a set of descriptive statistics that provide information about a dataset. 
It consists of five quartiles:

1. $Q_0$ the lowest value of the sample, aka **Minimum**
1. $Q_1$ $25\%$ of the sample, aka **lower quantile**
1. $Q_2$ $50\%$ of the sample, aka **median**
1. $Q_3$ $75\%$ of the sample, aka **upper quantile**
1. $Q_4$ the biggest value of the sample, aka **Maximum**

The functions `summary()` and `fivenum()` returns the five-number summary. They are a bit different how they calculate the $1^{st}$ and $3^{rd}$ quantiles.
`summary()` calculates the average of the two numbers, if even, `fivenum()` returns the minimum. 


```r
### The function summary will give you an overview of the full data set if you want to!
summary(data)
```

```
##          X        Age_death       Age_onset     Alcohol_abuse  
##  GSM123182: 1   Min.   :19.00   Min.   :14.00   Min.   :0.000  
##  GSM123183: 1   1st Qu.:38.00   1st Qu.:18.25   1st Qu.:0.000  
##  GSM123184: 1   Median :45.00   Median :22.00   Median :1.000  
##  GSM123185: 1   Mean   :44.18   Mean   :24.03   Mean   :1.817  
##  GSM123186: 1   3rd Qu.:49.00   3rd Qu.:27.75   3rd Qu.:3.250  
##  GSM123187: 1   Max.   :64.00   Max.   :45.00   Max.   :5.000  
##  (Other)  :55                   NA's   :31      NA's   :1      
##     Brain_ph                  Status     Drug_abuse     Duration_illness
##  Min.   :5.920   Bipolar disorder:30   Min.   :0.0000   Min.   : 2.00   
##  1st Qu.:6.400   Healthy control :31   1st Qu.:0.0000   1st Qu.:14.00   
##  Median :6.600                         Median :0.0000   Median :18.50   
##  Mean   :6.553                         Mean   :0.1935   Mean   :20.50   
##  3rd Qu.:6.740                         3rd Qu.:0.0000   3rd Qu.:27.75   
##  Max.   :7.030                         Max.   :3.0000   Max.   :45.00   
##                                        NA's   :30       NA's   :31      
##  Therapy_Electroconvulsive Therapy_Fluphenazine    Gender  
##  No  :28                   Min.   :     0       Female:21  
##  Yes : 2                   1st Qu.:     0       Male  :40  
##  NA's:31                   Median :  3000                  
##                            Mean   : 11283                  
##                            3rd Qu.: 12000                  
##                            Max.   :130000                  
##                            NA's   :32                      
##  Therapy_Lithium Post_morten_interval Side_brain Suicide 
##  No  :22         Min.   : 9.00        Left :32   No :49  
##  Yes : 8         1st Qu.:23.00        Right:29   Yes:12  
##  NA's:31         Median :31.00                           
##                  Mean   :33.05                           
##                  3rd Qu.:39.00                           
##                  Max.   :84.00                           
##                                                          
##  Therapy_Valproate     APOLD1          CLDN10          DUSP4      
##  No  :20           Min.   :1.765   Min.   :1.822   Min.   :1.445  
##  Yes :10           1st Qu.:1.917   1st Qu.:1.984   1st Qu.:1.522  
##  NA's:31           Median :1.957   Median :2.042   Median :1.562  
##                    Mean   :1.997   Mean   :2.042   Mean   :1.573  
##                    3rd Qu.:2.113   3rd Qu.:2.113   3rd Qu.:1.632  
##                    Max.   :2.286   Max.   :2.181   Max.   :1.718  
##                                                                   
##      EFEMP1          ETNPPL           GJA1           PLSCR4     
##  Min.   :1.585   Min.   :1.903   Min.   :1.840   Min.   :1.683  
##  1st Qu.:1.766   1st Qu.:2.115   1st Qu.:2.154   1st Qu.:1.861  
##  Median :1.847   Median :2.194   Median :2.235   Median :1.905  
##  Mean   :1.835   Mean   :2.183   Mean   :2.219   Mean   :1.903  
##  3rd Qu.:1.916   3rd Qu.:2.278   3rd Qu.:2.309   3rd Qu.:1.971  
##  Max.   :2.049   Max.   :2.367   Max.   :2.409   Max.   :2.079  
##                                                                 
##       SDC4          SLC14A1           SOX9            SST       
##  Min.   :1.740   Min.   :1.156   Min.   :1.819   Min.   :1.782  
##  1st Qu.:1.984   1st Qu.:1.291   1st Qu.:1.981   1st Qu.:2.083  
##  Median :2.028   Median :1.320   Median :2.063   Median :2.172  
##  Mean   :2.021   Mean   :1.340   Mean   :2.040   Mean   :2.137  
##  3rd Qu.:2.087   3rd Qu.:1.400   3rd Qu.:2.109   3rd Qu.:2.206  
##  Max.   :2.178   Max.   :1.669   Max.   :2.237   Max.   :2.277  
##                                                                 
##       TAC1           CX3CR1          DDX3Y          ETNPPL.1    
##  Min.   :1.549   Min.   :1.749   Min.   :1.397   Min.   :1.903  
##  1st Qu.:1.837   1st Qu.:1.865   1st Qu.:1.484   1st Qu.:2.115  
##  Median :1.911   Median :1.979   Median :1.764   Median :2.194  
##  Mean   :1.885   Mean   :1.974   Mean   :1.674   Mean   :2.183  
##  3rd Qu.:1.974   3rd Qu.:2.072   3rd Qu.:1.807   3rd Qu.:2.278  
##  Max.   :2.071   Max.   :2.263   Max.   :1.891   Max.   :2.367  
##                                                                 
##      G3BP2           GABRG2          KDM5D            MAFB      
##  Min.   :1.790   Min.   :1.722   Min.   :1.494   Min.   :1.660  
##  1st Qu.:1.937   1st Qu.:1.974   1st Qu.:1.677   1st Qu.:1.869  
##  Median :1.994   Median :2.088   Median :1.959   Median :1.919  
##  Mean   :1.988   Mean   :2.061   Mean   :1.863   Mean   :1.900  
##  3rd Qu.:2.044   3rd Qu.:2.166   3rd Qu.:2.000   3rd Qu.:1.951  
##  Max.   :2.117   Max.   :2.245   Max.   :2.054   Max.   :2.047  
##                                                                 
##       NBEA            OXR1            PAK1           PCDH8      
##  Min.   :1.868   Min.   :1.622   Min.   :1.333   Min.   :1.727  
##  1st Qu.:2.089   1st Qu.:1.896   1st Qu.:1.584   1st Qu.:1.941  
##  Median :2.110   Median :1.983   Median :1.709   Median :1.997  
##  Mean   :2.094   Mean   :1.951   Mean   :1.679   Mean   :1.986  
##  3rd Qu.:2.143   3rd Qu.:2.030   3rd Qu.:1.784   3rd Qu.:2.053  
##  Max.   :2.202   Max.   :2.151   Max.   :1.901   Max.   :2.113  
##                                                                 
##       PPID           PVALB           RPS4Y1          SST.1      
##  Min.   :1.808   Min.   :1.779   Min.   :1.640   Min.   :1.782  
##  1st Qu.:1.986   1st Qu.:1.987   1st Qu.:1.768   1st Qu.:2.083  
##  Median :2.017   Median :2.052   Median :2.227   Median :2.172  
##  Mean   :2.004   Mean   :2.030   Mean   :2.065   Mean   :2.137  
##  3rd Qu.:2.051   3rd Qu.:2.087   3rd Qu.:2.248   3rd Qu.:2.206  
##  Max.   :2.109   Max.   :2.154   Max.   :2.280   Max.   :2.277  
##                                                                 
##      TAC1.1         TBL1XR1          USP9Y            XIST      
##  Min.   :1.549   Min.   :1.504   Min.   :1.160   Min.   :1.424  
##  1st Qu.:1.837   1st Qu.:1.742   1st Qu.:1.293   1st Qu.:1.462  
##  Median :1.911   Median :1.805   Median :1.483   Median :1.497  
##  Mean   :1.885   Mean   :1.789   Mean   :1.454   Mean   :1.701  
##  3rd Qu.:1.974   3rd Qu.:1.856   3rd Qu.:1.589   3rd Qu.:2.106  
##  Max.   :2.071   Max.   :1.990   Max.   :1.796   Max.   :2.264  
## 
```


```r
### or just the variable you want.
summary(data$Age_death)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   19.00   38.00   45.00   44.18   49.00   64.00
```

```r
fivenum(data$Age_death)
```

```
## [1] 19 38 45 49 64
```

```r
###  (minimum, lower-hinge, median, upper-hinge, maximum)
```

The graphical representation of the summary is the `boxplot()`.


```r
boxplot(data$Age_onset, 
        las = 2, 
        pch = 4,
        col = 'salmon')
```

![](Material_BD_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

If we are interested in the summary **by** condition, we can use the function `by()`.


```r
by(data, data$Status, 
   FUN = summary)
```

```
## data$Status: Bipolar disorder
##          X        Age_death       Age_onset     Alcohol_abuse  
##  GSM123182: 1   Min.   :19.00   Min.   :14.00   Min.   :0.000  
##  GSM123183: 1   1st Qu.:41.00   1st Qu.:18.25   1st Qu.:1.000  
##  GSM123184: 1   Median :44.00   Median :22.00   Median :3.000  
##  GSM123185: 1   Mean   :44.53   Mean   :24.03   Mean   :2.862  
##  GSM123186: 1   3rd Qu.:50.75   3rd Qu.:27.75   3rd Qu.:5.000  
##  GSM123187: 1   Max.   :64.00   Max.   :45.00   Max.   :5.000  
##  (Other)  :24                                   NA's   :1      
##     Brain_ph                  Status     Drug_abuse  Duration_illness
##  Min.   :5.920   Bipolar disorder:30   Min.   : NA   Min.   : 2.00   
##  1st Qu.:6.355   Healthy control : 0   1st Qu.: NA   1st Qu.:14.00   
##  Median :6.505                         Median : NA   Median :18.50   
##  Mean   :6.475                         Mean   :NaN   Mean   :20.50   
##  3rd Qu.:6.665                         3rd Qu.: NA   3rd Qu.:27.75   
##  Max.   :6.970                         Max.   : NA   Max.   :45.00   
##                                        NA's   :30                    
##  Therapy_Electroconvulsive Therapy_Fluphenazine    Gender  
##  No :28                    Min.   :     0       Female:14  
##  Yes: 2                    1st Qu.:     0       Male  :16  
##                            Median :  3000                  
##                            Mean   : 11283                  
##                            3rd Qu.: 12000                  
##                            Max.   :130000                  
##                            NA's   :1                       
##  Therapy_Lithium Post_morten_interval Side_brain Suicide 
##  No :22          Min.   :12.00        Left :18   No :18  
##  Yes: 8          1st Qu.:23.25        Right:12   Yes:12  
##                  Median :35.00                           
##                  Mean   :37.17                           
##                  3rd Qu.:47.00                           
##                  Max.   :84.00                           
##                                                          
##  Therapy_Valproate     APOLD1          CLDN10          DUSP4      
##  No :20            Min.   :1.807   Min.   :1.889   Min.   :1.445  
##  Yes:10            1st Qu.:1.941   1st Qu.:2.006   1st Qu.:1.506  
##                    Median :1.983   Median :2.051   Median :1.540  
##                    Mean   :2.024   Mean   :2.062   Mean   :1.548  
##                    3rd Qu.:2.117   3rd Qu.:2.136   3rd Qu.:1.572  
##                    Max.   :2.286   Max.   :2.181   Max.   :1.718  
##                                                                   
##      EFEMP1          ETNPPL           GJA1           PLSCR4     
##  Min.   :1.616   Min.   :2.000   Min.   :2.014   Min.   :1.701  
##  1st Qu.:1.796   1st Qu.:2.163   1st Qu.:2.200   1st Qu.:1.890  
##  Median :1.867   Median :2.204   Median :2.260   Median :1.916  
##  Mean   :1.853   Mean   :2.215   Mean   :2.244   Mean   :1.921  
##  3rd Qu.:1.922   3rd Qu.:2.287   3rd Qu.:2.309   3rd Qu.:1.989  
##  Max.   :2.003   Max.   :2.367   Max.   :2.374   Max.   :2.078  
##                                                                 
##       SDC4          SLC14A1           SOX9            SST       
##  Min.   :1.855   Min.   :1.168   Min.   :1.819   Min.   :1.782  
##  1st Qu.:2.007   1st Qu.:1.309   1st Qu.:2.039   1st Qu.:2.032  
##  Median :2.058   Median :1.346   Median :2.087   Median :2.093  
##  Mean   :2.051   Mean   :1.363   Mean   :2.067   Mean   :2.084  
##  3rd Qu.:2.106   3rd Qu.:1.405   3rd Qu.:2.121   3rd Qu.:2.170  
##  Max.   :2.158   Max.   :1.583   Max.   :2.237   Max.   :2.277  
##                                                                 
##       TAC1           CX3CR1          DDX3Y          ETNPPL.1    
##  Min.   :1.549   Min.   :1.762   Min.   :1.397   Min.   :2.000  
##  1st Qu.:1.771   1st Qu.:1.826   1st Qu.:1.445   1st Qu.:2.163  
##  Median :1.849   Median :1.961   Median :1.723   Median :2.204  
##  Mean   :1.825   Mean   :1.940   Mean   :1.627   Mean   :2.215  
##  3rd Qu.:1.928   3rd Qu.:2.021   3rd Qu.:1.783   3rd Qu.:2.287  
##  Max.   :2.029   Max.   :2.149   Max.   :1.839   Max.   :2.367  
##                                                                 
##      G3BP2           GABRG2          KDM5D            MAFB      
##  Min.   :1.790   Min.   :1.722   Min.   :1.494   Min.   :1.660  
##  1st Qu.:1.903   1st Qu.:1.946   1st Qu.:1.626   1st Qu.:1.810  
##  Median :1.979   Median :2.050   Median :1.945   Median :1.900  
##  Mean   :1.958   Mean   :2.022   Mean   :1.814   Mean   :1.869  
##  3rd Qu.:2.015   3rd Qu.:2.117   3rd Qu.:1.994   3rd Qu.:1.945  
##  Max.   :2.107   Max.   :2.240   Max.   :2.034   Max.   :2.031  
##                                                                 
##       NBEA            OXR1            PAK1           PCDH8      
##  Min.   :1.868   Min.   :1.622   Min.   :1.333   Min.   :1.727  
##  1st Qu.:2.013   1st Qu.:1.845   1st Qu.:1.512   1st Qu.:1.906  
##  Median :2.101   Median :1.934   Median :1.659   Median :1.965  
##  Mean   :2.061   Mean   :1.916   Mean   :1.644   Mean   :1.953  
##  3rd Qu.:2.122   3rd Qu.:1.999   3rd Qu.:1.772   3rd Qu.:2.005  
##  Max.   :2.176   Max.   :2.101   Max.   :1.882   Max.   :2.076  
##                                                                 
##       PPID           PVALB           RPS4Y1          SST.1      
##  Min.   :1.808   Min.   :1.779   Min.   :1.685   Min.   :1.782  
##  1st Qu.:1.916   1st Qu.:1.941   1st Qu.:1.755   1st Qu.:2.032  
##  Median :1.996   Median :2.014   Median :2.201   Median :2.093  
##  Mean   :1.973   Mean   :2.000   Mean   :2.013   Mean   :2.084  
##  3rd Qu.:2.017   3rd Qu.:2.073   3rd Qu.:2.235   3rd Qu.:2.170  
##  Max.   :2.097   Max.   :2.112   Max.   :2.280   Max.   :2.277  
##                                                                 
##      TAC1.1         TBL1XR1          USP9Y            XIST      
##  Min.   :1.549   Min.   :1.504   Min.   :1.160   Min.   :1.434  
##  1st Qu.:1.771   1st Qu.:1.682   1st Qu.:1.283   1st Qu.:1.479  
##  Median :1.849   Median :1.775   Median :1.401   Median :1.534  
##  Mean   :1.825   Mean   :1.753   Mean   :1.404   Mean   :1.791  
##  3rd Qu.:1.928   3rd Qu.:1.834   3rd Qu.:1.526   3rd Qu.:2.127  
##  Max.   :2.029   Max.   :1.914   Max.   :1.680   Max.   :2.264  
##                                                                 
## -------------------------------------------------------- 
## data$Status: Healthy control
##          X        Age_death       Age_onset   Alcohol_abuse   
##  GSM123212: 1   Min.   :31.00   Min.   : NA   Min.   :0.0000  
##  GSM123213: 1   1st Qu.:38.00   1st Qu.: NA   1st Qu.:0.0000  
##  GSM123214: 1   Median :45.00   Median : NA   Median :0.0000  
##  GSM123215: 1   Mean   :43.84   Mean   :NaN   Mean   :0.8387  
##  GSM123216: 1   3rd Qu.:48.50   3rd Qu.: NA   3rd Qu.:1.0000  
##  GSM123217: 1   Max.   :59.00   Max.   : NA   Max.   :4.0000  
##  (Other)  :25                   NA's   :31                    
##     Brain_ph                  Status     Drug_abuse     Duration_illness
##  Min.   :6.000   Bipolar disorder: 0   Min.   :0.0000   Min.   : NA     
##  1st Qu.:6.500   Healthy control :31   1st Qu.:0.0000   1st Qu.: NA     
##  Median :6.700                         Median :0.0000   Median : NA     
##  Mean   :6.628                         Mean   :0.1935   Mean   :NaN     
##  3rd Qu.:6.810                         3rd Qu.:0.0000   3rd Qu.: NA     
##  Max.   :7.030                         Max.   :3.0000   Max.   : NA     
##                                                         NA's   :31      
##  Therapy_Electroconvulsive Therapy_Fluphenazine    Gender  
##  No  : 0                   Min.   : NA          Female: 7  
##  Yes : 0                   1st Qu.: NA          Male  :24  
##  NA's:31                   Median : NA                     
##                            Mean   :NaN                     
##                            3rd Qu.: NA                     
##                            Max.   : NA                     
##                            NA's   :31                      
##  Therapy_Lithium Post_morten_interval Side_brain Suicide 
##  No  : 0         Min.   : 9.00        Left :14   No :31  
##  Yes : 0         1st Qu.:21.50        Right:17   Yes: 0  
##  NA's:31         Median :28.00                           
##                  Mean   :29.06                           
##                  3rd Qu.:36.50                           
##                  Max.   :58.00                           
##                                                          
##  Therapy_Valproate     APOLD1          CLDN10          DUSP4      
##  No  : 0           Min.   :1.765   Min.   :1.822   Min.   :1.469  
##  Yes : 0           1st Qu.:1.889   1st Qu.:1.974   1st Qu.:1.538  
##  NA's:31           Median :1.942   Median :2.026   Median :1.618  
##                    Mean   :1.971   Mean   :2.022   Mean   :1.597  
##                    3rd Qu.:2.065   3rd Qu.:2.097   3rd Qu.:1.656  
##                    Max.   :2.284   Max.   :2.146   Max.   :1.707  
##                                                                   
##      EFEMP1          ETNPPL           GJA1           PLSCR4     
##  Min.   :1.585   Min.   :1.903   Min.   :1.840   Min.   :1.683  
##  1st Qu.:1.760   1st Qu.:2.067   1st Qu.:2.127   1st Qu.:1.848  
##  Median :1.804   Median :2.166   Median :2.199   Median :1.883  
##  Mean   :1.817   Mean   :2.152   Mean   :2.196   Mean   :1.885  
##  3rd Qu.:1.902   3rd Qu.:2.242   3rd Qu.:2.302   3rd Qu.:1.957  
##  Max.   :2.049   Max.   :2.329   Max.   :2.409   Max.   :2.079  
##                                                                 
##       SDC4          SLC14A1           SOX9            SST       
##  Min.   :1.740   Min.   :1.156   Min.   :1.842   Min.   :1.996  
##  1st Qu.:1.963   1st Qu.:1.269   1st Qu.:1.966   1st Qu.:2.172  
##  Median :2.003   Median :1.300   Median :2.018   Median :2.197  
##  Mean   :1.992   Mean   :1.318   Mean   :2.013   Mean   :2.189  
##  3rd Qu.:2.045   3rd Qu.:1.339   3rd Qu.:2.087   3rd Qu.:2.225  
##  Max.   :2.178   Max.   :1.669   Max.   :2.194   Max.   :2.275  
##                                                                 
##       TAC1           CX3CR1          DDX3Y          ETNPPL.1    
##  Min.   :1.760   Min.   :1.749   Min.   :1.440   Min.   :1.903  
##  1st Qu.:1.897   1st Qu.:1.923   1st Qu.:1.722   1st Qu.:2.067  
##  Median :1.945   Median :1.987   Median :1.770   Median :2.166  
##  Mean   :1.944   Mean   :2.006   Mean   :1.720   Mean   :2.152  
##  3rd Qu.:1.996   3rd Qu.:2.098   3rd Qu.:1.810   3rd Qu.:2.242  
##  Max.   :2.071   Max.   :2.263   Max.   :1.891   Max.   :2.329  
##                                                                 
##      G3BP2           GABRG2          KDM5D            MAFB      
##  Min.   :1.898   Min.   :1.840   Min.   :1.543   Min.   :1.760  
##  1st Qu.:1.959   1st Qu.:2.024   1st Qu.:1.921   1st Qu.:1.901  
##  Median :2.035   Median :2.139   Median :1.977   Median :1.938  
##  Mean   :2.018   Mean   :2.099   Mean   :1.909   Mean   :1.931  
##  3rd Qu.:2.069   3rd Qu.:2.180   3rd Qu.:2.000   3rd Qu.:1.961  
##  Max.   :2.117   Max.   :2.245   Max.   :2.054   Max.   :2.047  
##                                                                 
##       NBEA            OXR1            PAK1           PCDH8      
##  Min.   :2.029   Min.   :1.641   Min.   :1.431   Min.   :1.866  
##  1st Qu.:2.097   1st Qu.:1.963   1st Qu.:1.646   1st Qu.:1.987  
##  Median :2.134   Median :2.005   Median :1.723   Median :2.021  
##  Mean   :2.125   Mean   :1.985   Mean   :1.713   Mean   :2.019  
##  3rd Qu.:2.148   3rd Qu.:2.036   3rd Qu.:1.799   3rd Qu.:2.063  
##  Max.   :2.202   Max.   :2.151   Max.   :1.901   Max.   :2.113  
##                                                                 
##       PPID           PVALB           RPS4Y1          SST.1      
##  Min.   :1.916   Min.   :1.930   Min.   :1.640   Min.   :1.996  
##  1st Qu.:2.014   1st Qu.:2.003   1st Qu.:2.209   1st Qu.:2.172  
##  Median :2.045   Median :2.081   Median :2.233   Median :2.197  
##  Mean   :2.034   Mean   :2.060   Mean   :2.115   Mean   :2.189  
##  3rd Qu.:2.056   3rd Qu.:2.103   3rd Qu.:2.249   3rd Qu.:2.225  
##  Max.   :2.109   Max.   :2.154   Max.   :2.270   Max.   :2.275  
##                                                                 
##      TAC1.1         TBL1XR1          USP9Y            XIST      
##  Min.   :1.760   Min.   :1.632   Min.   :1.160   Min.   :1.424  
##  1st Qu.:1.897   1st Qu.:1.761   1st Qu.:1.393   1st Qu.:1.450  
##  Median :1.945   Median :1.825   Median :1.540   Median :1.484  
##  Mean   :1.944   Mean   :1.823   Mean   :1.502   Mean   :1.615  
##  3rd Qu.:1.996   3rd Qu.:1.879   3rd Qu.:1.607   3rd Qu.:1.537  
##  Max.   :2.071   Max.   :1.990   Max.   :1.796   Max.   :2.162  
## 
```

And the `boxplot()`:


```r
boxplot(data$Age_death ~ data$Status, 
        las = 1, pch = 4, 
        col = c('salmon', 'turquoise'))
```

![](Material_BD_files/figure-html/unnamed-chunk-11-1.png)<!-- -->


#### Mean
It is calculated by taking the sum of the values and dividing by the total number of values of the data. 
The function `mean()` can be used to calculate the average.



```r
mean(data$Age_death)
```

```
## [1] 44.18033
```

**Interpretation**: On average, the age of death of death in this study was 44.1803279.


You can also use `colMeans()` and `rowMeans()` to compute the mean of columns and rows.


```r
colMeans(data[,c(17:28)], na.rm = T)
```

```
##   APOLD1   CLDN10    DUSP4   EFEMP1   ETNPPL     GJA1   PLSCR4     SDC4 
## 1.997240 2.041634 1.572833 1.834842 2.183101 2.219369 1.902763 2.020990 
##  SLC14A1     SOX9      SST     TAC1 
## 1.339995 2.039741 2.137336 1.885281
```


```r
rowMeans(data[,17:28], na.rm = T)
```

```
##        1        2        3        4        5        6        7        8 
## 2.012922 1.832557 1.840020 1.924146 1.962231 1.981616 1.945479 1.928857 
##        9       10       11       12       13       14       15       16 
## 1.941477 2.000882 1.874723 1.904858 1.995695 1.974847 1.949153 1.918931 
##       17       18       19       20       21       22       23       24 
## 1.934290 1.987450 1.958006 1.968084 1.991263 1.920940 1.984736 1.907677 
##       25       26       27       28       29       30       31       32 
## 1.984528 1.895546 1.952010 1.934318 1.876073 1.858500 1.876398 1.942668 
##       33       34       35       36       37       38       39       40 
## 1.885970 1.878481 1.817041 1.931009 1.960738 1.963836 1.923612 1.764979 
##       41       42       43       44       45       46       47       48 
## 1.916670 1.990174 1.864843 1.881532 1.986587 2.011588 1.996816 1.922206 
##       49       50       51       52       53       54       55       56 
## 1.888220 1.991758 1.944774 1.978649 1.859148 1.978678 1.982875 1.994539 
##       57       58       59       60       61 
## 1.851342 2.051429 1.809602 1.921640 1.897267
```

#### Median
It is the value that divides your dataset in the **middle**, the function`median()` computes it in R.


```r
median(data$Age_death)
```

```
## [1] 45
```

**Interpretation**: The mid-age of death in this study was 45. This means that this is the value that divides the data in half.

Note! The median is not always the same as the mean!


```r
# attach(data)
plot((density(data$Age_death))$x,(density(data$Age_death))$y, 
     type = "l", 
     xlab = "Age of death",
     ylab = "Density", 
     las = 2, 
     axes = F, 
     col = "violet", 
     lwd = 3, 
     col.lab = "steelblue")
axis(1, 
     at = seq(from = (round(min(data$Age_death))-2), by = 2, to = (2+round(max(data$Age_death)))), 
     col = "gray75",  
     col.axis = "gray75")
axis(2, 
     las = 2, 
     col = "gray75", 
     col.ticks = "gray75", 
     col.axis = "gray75")
abline(v = median(data$Age_death), 
       col = "salmon", 
       lwd = 2)
abline(v = mean(data$Age_death), 
       col = "steelblue", 
       lwd = 2)
legend("topright", 
       c(paste("Median", round(median(data$Age_death),2)), paste("Mean", round(mean(data$Age_death),2))), 
       col = c("salmon", "steelblue"), 
       lwd = 2, 
       bty = "n")
```

![](Material_BD_files/figure-html/unnamed-chunk-16-1.png)<!-- -->

#### Mode

The mode is the value that appears **the most on a variable**. 
It's the *fashion* of the variable, the one that almost everyone is wearing. 
And sometimes there might exist more than one mode, but we won't deal with it here.

Unfortunately, there is no function on R to compute the mode. But we already know how to construct tables and functions. So, let's create our own function.


```r
Mode = function(VAR){
    actual_mode <- table(VAR) 
    NAME = names(actual_mode)[actual_mode == max(actual_mode)]
    VAL = actual_mode[actual_mode == max(actual_mode)]
    return( data.frame(Value = NAME, Frequency = VAL))
}
```



```r
Mode(data$Age_onset)
```

<div class="kable-table">

     Value    Frequency
---  ------  ----------
25   25               3

</div>

**Interpretation**: The most common age where people were diagnosed with bipolar disorder was 25 years.



### Dispersion measures
The dispersion refers to *how the values are spread* from the central data. It is as important as the central tendency values. Some of them are *Range*, *IQR*, *Variance* and *standard deviation*.

#### Range
The range is the difference between the largest and the smallest value of a variable.
The function `range()` returns the minimum and maximum values, to have the range we have to use the function `diff()`.


```r
range(data$Age_death) %>% 
  diff(.)
```

```
## [1] 45
```

**Interpretation**: The difference (or the range) among the ages in our study was 45 years.

#### Interquartile range (IQR)
Is the difference between the third and the first quartiles. It's used to read and draw the boxplots.
On R we can use the function IQR()

```r
IQR(data$Age_death)
```

```
## [1] 11
```


**Interpretation**: The Interval interquantile is 25. This value in general used to interpret the `boxplot()`.


```r
boxplot(data$Age_death , 
        col = c ("springgreen"), 
        ylab = "Age of death - years", 
        las = 2)
```

![](Material_BD_files/figure-html/unnamed-chunk-21-1.png)<!-- -->

```r
quantile(data$Age_death)
```

```
##   0%  25%  50%  75% 100% 
##   19   38   45   49   64
```



#### Variance
Variance is the expectation of the squared deviation of a random variable from its mean. In other words, the variance is how far the values are from the mean. We can calculate it using the function `var()`.


```r
var(data$Age_death)
```

```
## [1] 82.38361
```

**Interpretation**: The age of the individuals in our study deviates from the mean in 82.3836066 years$^2$ (quadratic scale).
However, it is quite hard to make interpretations in years$^2$.

#### Standard deviation (SD)
Is how the values are spread around the mean. It is given by the square root of the variance. A low standard deviation indicates that the data points tend to be close to the mean, while a high standard deviation indicates that the data points are spread out over a wider range of values. To compute the SD using R we can use the function `sd()`.

```r
sd(data$Age_death)
```

```
## [1] 9.076542
```

```r
sqrt(var(data$Age_death))
```

```
## [1] 9.076542
```

**Interpretation**: The age of the individuals in our study deviates from the mean in 9.0765416 years (data scale).

#### Coefficient of variation
The coefficient of variation is the sd divided by the mean. In chemistry is widely used to express the precision and repeatability of an experiment. Normally it is expressed as a percentage. The CV aims to describe the dispersion of the variable in a way that does not depend on the variable's measurement unit. The higher the CV, the greater the dispersion in the variable.

It's also used to compare the variation of variables that are not on the same scale.

The R doesn't have a function to compute it, but we can easily create our own.


```r
sd(sd(data$Age_death))/mean(sd(data$Age_death))*100
```

```
## [1] NA
```



```r
CV <- function(VAR){
      (sd(VAR, na.rm = T)/mean(VAR, na.rm = T))*100
      }
```


```r
CV(sd(data$Age_death))
```

```
## [1] NA
```


### Exercises
1. Make absolute and relative frequency tables for the variables: Gender and Status. 

1. Check how many people that suffered from Bipolar Disorder committed suicide. Visualise it using a barplot. 

2. Compute and make the correct interpretations for the mean, median, IQR, standard deviation, variance and CV for all the quantitative variables. Tipp: You can create a function for that!

3. Compute and make the correct interpretations for the mean, median, IQR, standard deviation, variance and CV for all the numeric variables only for Males. Tipp: Use the function created before on a subset of the dataset.

4. Compute and make the correct interpretations for the mean, median, IQR, standard deviation, variance and CV for all the quantitative variables for both conditions separately.



## Inference and Probability
**Inference** is the part of statistics that make inference about features from a sample to a population. 

A population is the **whole group you are interested in**, and the size doesn't matter! So, let's say I'm interested in the age of the students from the EVOP $2019$, the population is all the students attending the course. 
Descriptive statistics that are applied to populations, for example, the mean or standard deviation, are called parameters as they represent the whole population.

Because we cannot always use the whole population, we can use a sample. 
The properties of the sample are called *statistics*. 
Because we won't have the same sample all the time, there is an error and a variability included in every sampling. 
**Therefore we don't expect our sample to be the same and represent perfectly our population.**

### Probability
Probability is a measure of **how likely some event is to occur**. It's a number **between 0 (impossible) and 1 (certain)**.

Example: How likely is to flip a fair coin and get heads? We have Heads and Tails, so $2$ possible outcomes, and our desired outcome is head. So, flipping only one fair coin, we have the probability of $1/2$ of getting head.

A **distribution** is a mathematical **function** that can be thought of as providing the probability of occurrence of different possible outcomes in an experiment. 
For each value on our study, there is a probability associated with it. An easy way to visualise it is making a histogram, for example.


```r
par(mfrow = c(2,2))
hist(data$Age_death, col = "skyblue", freq = F, las = 1, main = 'Age of death')
hist(data$Brain_ph, col = "orange", freq = F, las = 1, main = 'Brain pH')
hist(data$Therapy_Fluphenazine, col = "lightpink", freq = F, las = 1, main = 'Fluphenazine (mg)')
hist(data$Age_onset, col = "gray25", freq = F, las = 1, main = 'Age on diagnosis')
```

![](Material_BD_files/figure-html/unnamed-chunk-27-1.png)<!-- -->

```r
par(mfrow = c(1,1))
```

Another way to plot the density function of a variable is to use the function `density()`.


```r
par(mfrow = c(2,2))

hist(data$Age_death, col = "skyblue", 
     freq = F, las = 1,
     main = 'Age of death')
lines(density(data$Age_death)$x, 
      density(data$Age_death)$y, 
      col = "blue")

hist(data$Brain_ph, col = "orange", 
     freq = F, las = 1, 
     main = 'Brain pH')
lines(density(data$Brain_ph)$x, 
      density(data$Brain_ph)$y, 
      col = "yellow")

hist(data$Therapy_Fluphenazine,
     col = "lightpink", freq = F,
     las = 1, main = 'Fluphenazine (mg)')
lines(density(data$Therapy_Fluphenazine, na.rm = T)$x, density(data$Therapy_Fluphenazine, na.rm = T)$y, col = "red")

hist(data$Age_onset, col = "gray25", 
     freq = F, las = 1, 
     main = 'Age on diagnosis')
lines(density(data$Age_onset, na.rm = T)$x, 
      density(data$Age_onset, na.rm = T)$y, 
      col = "gray25")
```

![](Material_BD_files/figure-html/unnamed-chunk-28-1.png)<!-- -->

```r
par(mfrow = c(1,1))
```


There is an infinity of distributions, some are very well known, and we will briefly talk about them.

Each distribution has its own characteristics, that gives its shape and form. For example, to describe a Normal distribution, we need its mean and variance.

#### Discrete Distributions
If a variable can assume just integers numbers, or it's counting than its distribution is discrete. Some examples of discrete distributions are: **Discrete Uniform**, **Poisson**, **Bernoulli**, **Binomial** etc.

They can be simulated on R using the functions: `runif()`, `rbinom()`, `rnbinom()`, `rpois()`.

* **Uniform**: All the values inside this distribution have the same probability to happen. Notation: $X\sim U(a,b)$, $a\leqslant b$.


```r
seq(0, 10) %>% dunif(.,min = 0, max = 10) %>%
plot(.,  type = "p",
     las = 1, 
     col = "red", 
     pch = 20, 
     main = "Uniform X ~ U(0,10)",
     xlab = "X values", 
     ylab ="Probability")
```

![](Material_BD_files/figure-html/unnamed-chunk-29-1.png)<!-- -->


* **Bernoulli**: This is an outcome of *one* yes/no experiment. Notation: $X\sim Bernoulli(p)$.

* **Binomial**: This is conventionally interpreted as the number of *successes* in *n* Bernoulli experiments. Notation: $X\sim Bin(n,p)$.


```r
rbinom( n=3, size = 100, prob = 0.5)
```

```
## [1] 54 47 50
```



```r
seq(0, 100) %>% dbinom(.,100,0.2) %>%
plot(.,  type = "p",
     col = "violetred", 
     pch = 20, 
     main = "Binomial",
     xlab = "X values", 
     ylab ="Probability", 
     xlim = c(0,100), 
     ylim = c(0,0.12), axes = F )

seq(0, 100) %>% dbinom(.,100,0.5) %>%
lines(.,  type = "p",col = "springgreen", pch =16)

seq(0, 100) %>% dbinom(.,100,0.8) %>%
lines(.,  type = "p",col = "steelblue", pch =18)

legend("topright", c("X ~ Bin(100,0.2)", 
                     "X ~ Bin(100,0.5)", 
                     "X ~ Bin(100,0.8)"), 
       lwd = c(2,2,2), col = c("violetred", 
                               "springgreen", 
                               "steelblue"), 
       bty="n" )
axis(1, las = 2)
axis(2, las = 2)
```

![](Material_BD_files/figure-html/unnamed-chunk-31-1.png)<!-- -->

* **Negative Binomial**: This is conventionally interpreted as the *number of successes in a sequence of independent and identically distributed Bernoulli trials **before** a specified (non-random) number of failures (denoted r) occurs*. Notation: $X\sim NegBin(p,r)$.


```r
rnbinom(n = 3, size = 100, prob = 0.5)
```

```
## [1] 129 100  94
```


```r
seq(0, 300) %>% dnbinom(.,100,0.4) %>%
plot(., 
     type ="p",col = "violetred", pch =20, 
     main = "Negative Binomial", xlab = "X values", 
     ylab ="Probability", xlim = c(0,300), ylim = c(0,0.05), axes = F)

seq(0, 300) %>% dnbinom(.,100,0.5) %>%
lines(., type = "p",col = "springgreen", pch = 16)

seq(0, 300) %>% dnbinom(.,100,0.6) %>%
lines(., type = "p",col = "steelblue", pch = 18)
legend("topright", c("X ~ NegBin(100,0.4)",
                     "X ~ NegBin(100,0.5)",
                     "X ~ NegBin(100,0.6)"), 
       lwd = c(2,2,2), col = c("violetred", "springgreen", "steelblue"), bty="n" )
axis (1, las = 1)
axis (2, las = 2)
```

![](Material_BD_files/figure-html/unnamed-chunk-33-1.png)<!-- -->

* **Poisson**: Expresses the probability of a given *number of events occurring in a fixed interval of time and/or space* if these events occur with a known average rate and independently of the time since the last event. Notation: $X\sim P(\lambda)$.


```r
rpois(n = 3, lambda = 3)
```

```
## [1] 3 4 3
```


```r
seq(0, 100) %>% dpois(.,1) %>%
plot(.,
     type = "p",
     col = "violetred", 
     pch = 20, 
     main = "Poisson", 
     xlab = "X values", 
     ylab = "Probability", 
     xlim = c(0,15), 
     ylim = c(0,0.5),
     axes = F)

seq(0, 100) %>% dpois(.,2) %>%
lines(.,
      type = "p",
      col = "springgreen", 
      pch =16)

seq(0, 100) %>% dpois(.,5) %>%
lines(.,
      type = "p",
      col = "steelblue", 
      pch =18)

legend("topright", 
       c("X ~ P(1)", "X ~ P(2)", "X ~ P(5)"), 
       pch = c(20,16,18), 
       col = c("violetred", "springgreen", "steelblue"), 
       bty = "n")

axis (1, las = 1)
axis (2, las = 2)
```

![](Material_BD_files/figure-html/unnamed-chunk-35-1.png)<!-- -->



#### Continuous distributions

If a variable cannot assume just integers numbers than its distribution is continuous. One continuous distribution doesn't have a probability associated to a certain point, but to a range. Some examples of continuous distributions are: **Uniform**, **Gamma**, **Normal**, **Exponential** etc.

They can be simulated on R using `runif()`, `rexp()`, `rgamma()`, `rnorm()`.

* **Uniform**: All the intervals of the same length are equally probable. Notation: $X\sim U(a,b)$, $a\leqslant b$.


```r
runif(n = 3, min =  1, max = 10)
```

```
## [1] 6.194879 9.122172 3.220966
```


```r
seq(0, 1, length.out = 50) %>% dunif(.) %>%
plot(x = seq(0, 1, length.out = 50),
     y = ., 
     col = "violetred",
     type = "l",
     lwd = 2, 
     las = 1,
     main = "Uniform", 
     xlab = "X values", 
     ylab ="Density", 
     xlim = c(-1,5), 
     ylim = c(0,1), 
     xaxs = "i")

seq(0, 4, length.out = 50) %>% dunif(., min = 0, max = 4) %>%
lines(x = seq(0, 4, length.out = 50),
      y = ., 
      col = "springgreen",
      lwd = 2)

seq(1, 3, length.out = 50) %>% dunif(., min = 1, max = 3) %>%
lines(x = seq(1, 3, length.out = 50),
      y = ., 
      col = "steelblue",
      lwd = 2)

legend("topright", 
       c("X ~ U(0,1)", "X ~ U(0,4)", "X ~ U(1,3)"), 
       lwd = 2,
       col = c("violetred", "springgreen", "steelblue"), 
       bty = "n")
```

![](Material_BD_files/figure-html/unnamed-chunk-37-1.png)<!-- -->

* **Exponential**: It is often used to model the **time elapsed between events**. It also is independent of the last step. Notation: $X\sim Exp(\lambda)$.


```r
rexp(n = 3, rate = 3)
```

```
## [1] 0.5971039 0.7919187 0.2318062
```


```r
seq(0, 100, length.out = 500) %>% dexp(., 3) %>%
plot(x = seq(0, 100, length.out = 500),
     y = .,
     col = "violetred",
     type = "l",
     lwd = 2, 
     las = 1,
     main = "Exponencial", 
     xlab = "X values", 
     ylab ="Density", 
     xlim = c(0,8), 
     ylim = c(0,3), 
     xaxs = "i")

seq(0, 100, length.out = 500) %>% dexp(.,1) %>%
lines(x = seq(0, 100, length.out = 500),
      y = .,
      col = "springgreen", 
      lwd = 2)

seq(0, 100, length.out = 500) %>% dexp(.,0.5) %>%
lines(x = seq(0, 100, length.out = 500),
      y = ., 
      col = "steelblue", 
      lwd = 2)

legend("topright", 
       c("X ~ Exp(0.5)", "X ~ Exp(1.0)", "X ~ Exp(3.0)"),
       col = c("steelblue", "springgreen", "violetred"),
       lwd = 2,
       bty = "n")
```

![](Material_BD_files/figure-html/unnamed-chunk-39-1.png)<!-- -->

* **Gamma**: Gamma is used to model **time to death or failure**. Notation: $X\sim G(\alpha,\beta)$, $\alpha,\beta \gt 0$.


```r
rgamma(n = 3, shape = 3, scale =  3)
```

```
## [1]  4.770358 12.625383 15.551681
```


```r
seq(0, 100, length.out = 500) %>% dgamma(.,10,2) %>%
plot(x = seq(0, 100, length.out = 500),
     y = .,
     col = "violetred",
     lwd = 2,
     las = 1,
     type = "l",
     main = "Gamma", 
     xlab = "X values", 
     ylab ="Density", 
     xlim = c(0,20), 
     ylim = c(0,0.8), 
     xaxs = "i")

seq(0, 100, length.out = 500) %>% dgamma(.,1.5,1) %>%
lines(x = seq(0, 100, length.out = 500),
      y = ., 
      col = "springgreen", 
      lwd = 2)

seq(0, 100, length.out = 500) %>% dgamma(.,5,3) %>%
lines(x = seq(0, 100, length.out = 500),
      y = ., 
      col = "steelblue", 
      lwd = 2)

seq(0, 100, length.out = 500) %>% dgamma(.,8,1) %>%
lines(x = seq(0, 100, length.out = 500),
      y = ., 
      col = "orange", 
      lwd = 2)

seq(0, 100, length.out = 500) %>% dgamma(.,2,0.4) %>%
lines(x = seq(0, 100, length.out = 500),
      y = .,
      col = "salmon", 
      lwd = 2)

legend("topright", c("X ~ G(10,2)",
                     "X ~ G(1,0.5)",
                     "X ~ G(5,3)", 
                     "X ~ G(8,1)", 
                     "X ~ G(2,0.4)"),
       lwd = 2,
       col = c("violetred", "springgreen", "steelblue", "orange", "salmon"), 
       bty = "n")
```

![](Material_BD_files/figure-html/unnamed-chunk-41-1.png)<!-- -->

* **Normal**: It is one the most important distributions, mainly because of the central limit theorem, which states that averages of random variables independently drawn from independent distributions converge in distribution to the normal, that is, become normally distributed when the number of random variables is sufficiently large. The normal distribution is symmetric about its mean, the mean is the same as median and mode. Notation: $X\sim N(\mu,\sigma)$.


```r
rnorm(n=3, mean = 0, sd = 1)
```

```
## [1]  1.5535167632 -1.0875741361 -0.0009670867
```



```r
seq(-10, 10, length.out = 500) %>% dnorm(.) %>%
plot(x = seq(-10, 10, length.out = 500), y = ., 
     col = "violetred",
     lwd = 2, 
     type = "l",
     main = "Normal", 
     xlab = "X values", 
     ylab = "Density", 
     xlim = c(-10,10), 
     #ylim = c(0,1), 
     xaxs = "i")

seq(-10, 10, length.out = 500) %>% dnorm(.,2,2) %>%
lines(x = seq(-10, 10, length.out = 500),
      y = .,
      col = "springgreen", 
      lwd = 2)

seq(-10, 10, length.out = 500) %>% dnorm(.,0,4) %>%
lines(x = seq(-10, 10, length.out = 500),
      y = .,
      col = "steelblue", 
      lwd = 2)

legend("topright", c("X ~ N(0,1)",
                     "X ~ N(2,2)",
                     "X ~ N(0,4)"),
       lwd = 2,
       col = c("violetred", "springgreen", "steelblue"), 
       bty = "n")
```

![](Material_BD_files/figure-html/unnamed-chunk-43-1.png)<!-- -->

### Hypothesis testing
A statistical hypothesis is an assumption about a population parameter. This assumption may or may not be true.
In traditional / frequentist Statistics there are two different statistical hypotheses:

* **Null hypothesis**: denoted by $H_0$ or H0, is usually the hypothesis that sample observations result **purely from chance**. In general, it is an equality assumption.

* **Alternative hypothesis**: denoted by $H_1$ or $H_a$, is the hypothesis that sample observations are influenced by some non-random cause. In general, it is inequality, superiority or inferiority.

There are two outcomes from Hypothesis testing:

1. **you reject the null hypothesis**;
1. **you fail to reject the null hypothesis**. We never say that we accepted the null hypothesis. Failure to reject means that the data are not sufficiently persuasive for us to prefer the alternative hypothesis over the null hypothesis.

#### Steps to test a null hypothesis

1. **State the hypotheses**. This involves stating the null and alternative hypotheses. The hypotheses are stated in such a way that they are mutually exclusive. That is, if one is true, the other must be false.
2. **Define the analysis plan**. The analysis plan describes how to use sample data to evaluate the null hypothesis. The evaluation often focuses on a single test statistic.
3. **Analyse sample data**. Find the value of the test statistic (mean score, proportion, t statistic, z-score, etc.) described in the analysis plan. At this point, you also determine your significance level.
4. **Make the interpretation of the results**. Apply the decision rule described in the analysis plan. If the value of the test statistic is improbable, based on the null hypothesis, reject the null hypothesis.

The hypothesis is measured using a statistic, that, in other words, is a probabilistic way to measure it.

* **Example**: We want to know if the two groups in our example are different. How different, in which sense? Let's say mean of ages. If they are not the same under H0, it means, the mean of both groups is different, the probability of having the same mean is small.

Again. We have 4 steps:

1. What is the hypothesis?
1. What is the statistics?
1. What is the significance level?
1. Results? Interpretation.

#### Type I and type II errors
Two types of errors are possible are possible from testing a hypothesis: **type I** and **type II**. 
The risks of these two errors are inversely related and determined by the **level of significance** and the **power of the test**.

* **Type I error**: occurs when the **null hypothesis is rejected when it is true**. The probability of committing a Type I error is called the significance level. This probability is also called alpha and is often denoted by $\alpha$.

* **Type II error**: occurs when the **null hypothesis is failed to be rejected when it is false**. The probability of committing a Type II error is called Beta and is often denoted by $\beta$. The probability of not committing a Type II error is called the Power of the test.

| Error types                         |                | Null hypothesis (H0) is                                   |                                                           |
|-------------------------------------|----------------|-----------------------------------------------------------|-----------------------------------------------------------|
|                                     |                | TRUE                                                      | FALSE                                                     |
| Decision About Null Hypothesis (H0) | Reject         | Type I error (False Positive) Probability = $\alpha$      | Correct inference (True Negative) Probability = $1 - \beta$ |
|                                     | Fail to reject | Correct inference (True Positive) Probability = $1 - \alpha$ | Type II error (False Negative) Probability = $\beta$      |

![](Teste_hip-b.png)

![](Teste_hip.png)



#### p-value
The **p-value** is defined as the probability of obtaining a result equal to or *more extreme* than what was observed, *when the null hypothesis is true*. In other words, it is *the strength of evidence in support of a null hypothesis*.

**Note: Because p-value is a probability it lies in the interval $(0,1)$.**

#### Confidence Intervals
Suppose we want to estimate an actual population mean. However, we cannot compute the population mean, right? What we have is a random sample from a population. Therefore, we have a **range where the real mean is, this range is called Confidence Interval**.

mean - value < $\mu$ < mean + value

This value depends on the confidence we want, also depends on the distribution chosen to describe the data.
For the **Normal** we have the probability for the desired quantile (1.96 for 5% significance) * the standard deviation of the sample/sqrt (size of the sample).

$IC = \bar{x} \pm 1.96 \times sd(x)/ \sqrt{n}$


```r
normal = rnorm(1000, 0, 1)
hist(normal, 
     las = 1, 
     col = rainbow(10),
     xlim = c(-4,4),
     main = "Histogram",
     xlab = "Normal Variable")
```

![](Material_BD_files/figure-html/unnamed-chunk-44-1.png)<!-- -->

```r
mean(normal)
```

```
## [1] -0.03859832
```

* Example: Suppose that we have 100 random samples (of size n = 25) that came from a certain population (of size N = 1000). In approximately 95% of the cases, the real mean will lie inside the estimated confidence interval.


```r
CI = function(data, sign=0.05, two.tails = T,size=25, n){
  CI = matrix(NA, nrow = n, ncol = 3)
  sign = ifelse(two.tails == T, sign/2, sign)
  for (i in 1:n){
    x =  sample(data, size = size)
    CI[i,1] = mean(x) 
    CI[i,2] = mean(x) + qnorm(sign) * sd(x)/sqrt(size)
    CI[i,3] = mean(x) - qnorm(sign) * sd(x)/sqrt(size)
  }
  CI = as.data.frame(CI)
  names(CI) = c("mean", "CI.lower", "CI.upper")
  return(CI)
}
```



```r
CIs = CI(data = normal, n = 100)

plot(CIs$mean ~ c(1:100),
     xlim = c(1,100),
     ylim = c(min(c(CIs$CI.lower, CIs$CI.upper)), 
              max(CIs$CI.lower, CIs$CI.upper)),xlab='',
     ylab='X', 
     main=paste('Confidence Interval for mean, n = 25'),
     col='grey75',pch=20, bg = "transparent", las = 1, axes = FALSE)
axis(1)
axis(2, las = 1)
cor = ifelse(mean(normal) < CIs$CI.lower | mean(normal) > CIs$CI.upper,  "red", "grey30")
arrows(c(1:100),CIs$CI.lower,c(1:100),CIs$CI.upper,code=3,length=0.2,angle=90,col=cor)
abline(h=mean(normal), lwd= 2, lty=2, col= "purple")
```

![](Material_BD_files/figure-html/unnamed-chunk-46-1.png)<!-- -->

#### Testing normality
Tests for normality are mainly used to check if a particular **variable follows a Normal distribution**. It is useful mainly because the **parametric tests require normality**.

A good test is the Shapiro Wilks test, on R can be used by the function `shapiro.test()`.

**Hypothesis:**

$H_0$: The variable X follows a Normal distribution

$H_a$: The variable X does not follow a Normal distribution

Notice that using the `shapiro.test()` we want the null hypothesis ($H_0$) to be true, so, we want high p-values.

We can also use `qqnorm()` to verify the quantiles of our distribution.

Example 1. Verify if the Age follows a Normal distribution.


```r
shapiro.test(data$Age_death)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  data$Age_death
## W = 0.98884, p-value = 0.8531
```

```r
par(mfrow = c(1,2))

qqnorm(data$Age_death, pch = 16)
qqline(data$Age_death, col = 2)
boxplot(data$Age_death)
```

![](Material_BD_files/figure-html/unnamed-chunk-48-1.png)<!-- -->

1. What is the hypothesis?
    * $H_0$: The Age follows a Normal distribution
    * $H_a$: The Age does not follow a Normal distribution
1. What is the statistics?
    * W.
1. What is the significance level?
    * 0.05
1.  Results?
    * p-value = `0.8531134`

**Interpretation**: The p-value is higher than 0.05, we assume that the Age of death follows a Normal distribution.


#### Testing variance
F-Test and Bartlett-Test are used to test if two variances are statistically equal.
Both tests assume that your variable has a Normal distribution.
Comparing two variances is useful in several cases, including:

1. When you want to perform a *two samples t-test* to check the equality of the variances of the two samples;
1. When you want to compare the variability of a new measurement method to an old one. Is the new method able to reduce the variability of the measure?

Hypothesis:

$H_0: \sigma_1 = \sigma_2$

$H_a: \sigma_1 \neq \sigma_2$

On R the functions `var.test()` or `bartlett.test()` can be used to test if two variables have the same variance.

* Example 1. Under a significance level of 5%, is the variance from Age of death in both groups the same?


```r
shapiro.test(data$Age_death)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  data$Age_death
## W = 0.98884, p-value = 0.8531
```

```r
var.test(data$Age_death[data$Status == 'Healthy control'], 
         data$Age_death[data$Status != 'Healthy control'])
```

```
## 
## 	F test to compare two variances
## 
## data:  data$Age_death[data$Status == "Healthy control"] and data$Age_death[data$Status != "Healthy control"]
## F = 0.46806, num df = 30, denom df = 29, p-value = 0.0427
## alternative hypothesis: true ratio of variances is not equal to 1
## 95 percent confidence interval:
##  0.2237046 0.9748117
## sample estimates:
## ratio of variances 
##          0.4680608
```


1. What is the hypothesis?
    * $H_0: \sigma_{Age_{Controls}} = \sigma_{Age_{Patients}}$
    * $H_a: \sigma_{Age_{Controls}} \neq \sigma_{Age_{Patients}}$
1. What is the statistics?
    * Variance
1. What is the significance level?
    * 0.05
1. Results?
    * p-value = `0.042697`


**Interpretation**: Because the p-value is less than 0.05, we can assume that both groups do not have the same variance.

* Example 2. Under a significance level of 5%, is there a difference in the variance of the age of death in gender groups?


```r
shapiro.test(data$Age_death[data$Gender == 'Female'])
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  data$Age_death[data$Gender == "Female"]
## W = 0.97069, p-value = 0.7483
```

```r
shapiro.test(data$Age_death[data$Gender != 'Female'])
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  data$Age_death[data$Gender != "Female"]
## W = 0.97729, p-value = 0.59
```

```r
var.test(data$Age_death ~ data$Gender)
```

```
## 
## 	F test to compare two variances
## 
## data:  data$Age_death by data$Gender
## F = 0.80358, num df = 20, denom df = 39, p-value = 0.6112
## alternative hypothesis: true ratio of variances is not equal to 1
## 95 percent confidence interval:
##  0.3868262 1.8419025
## sample estimates:
## ratio of variances 
##          0.8035782
```

```r
bartlett.test(data$Age_death ~ data$Gender)
```

```
## 
## 	Bartlett test of homogeneity of variances
## 
## data:  data$Age_death by data$Gender
## Bartlett's K-squared = 0.30237, df = 1, p-value = 0.5824
```

1. What is the hypothesis?
    * $H_0: \sigma_{Age_{Female}} = \sigma_{Age_{Male}}$
    * $H_a: \sigma_{Age_{Female}} \neq \sigma_{Age_{Male}}$
1. What is the statistic?
    * Variance
1. What is the significance level?
    * 0.05
1. Results?
    * p-value = `0.6111912`

**Interpretation**: The p-value is greater than 0.05, we cannot discard the null hypothesis, it means that we don't have enough support to say that the variance in both groups differs.


#### Testing the mean
To test if the mean of one variable is the same among two groups, one can use the t-test. You can also use the t-test to test if a certain sample mean is a particular number.

Important: 
* ** The t-test needs that the variable of interest follows a Normal distribution.**
* If you want to compare means of more than 2 groups you should use ANOVA.
* There are 4 different t-tests, and we can use the function `t.test()` for all t.tests.

##### Test if a certain value is the mean of a certain variable
One sample t-test: You want to check whether a certain value $(x)$ is plausible or not for the real (populational) mean.
** One sample has to be independent of the other **.
Hypothesis:

$H_0: \mu = x$

$H_a: \mu \neq x$

* Example: We believe that the average age of death in our population is 40 years old, using the significance level of 0.05 test our hypothesis.



```r
### First we have to test for normality!
shapiro.test(data$Age_death)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  data$Age_death
## W = 0.98884, p-value = 0.8531
```

```r
## It's Normal! Great, let's follow!

t.test(data$Age_death, mu = 40, data = data)
```

```
## 
## 	One Sample t-test
## 
## data:  data$Age_death
## t = 3.5971, df = 60, p-value = 0.000652
## alternative hypothesis: true mean is not equal to 40
## 95 percent confidence interval:
##  41.85572 46.50494
## sample estimates:
## mean of x 
##  44.18033
```

1. What is the hypothesis?
    * $H_0: \mu_{Age} = 40$
    * $H_a: \mu_{Age} \neq 40$
1. What is the statistics?
    * Mean
1. What is the significance level?
    * 0.05
1. Results?
    * p-value < 0.001
    
**Interpretation**: The p-value is smaller than 0.05, we discard the null hypothesis and accept the alternative one, it means that the mean of Age is different than 40 years old.

##### Test if two samples (with the same variance) have the same mean

Two samples, with the same variance: You want to know if 2 groups with the same variance have the same mean.
One sample must be independent of the other.

Hypothesis:

$H_0: \mu_{1} = \mu_{2}$

$H_a: \mu_{1} \neq \mu_{2}$

* Example: You want to know if the age of death is different in the two genders. Use the significance level of 0.05.


```r
## Again, we also have to test for normality, but we did this before for this variable!
shapiro.test(data$Age_death)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  data$Age_death
## W = 0.98884, p-value = 0.8531
```

```r
# It's Normal, great! We have now to test for the variance in the two groups. 
var.test(Age_death ~ Status, data = data)
```

```
## 
## 	F test to compare two variances
## 
## data:  Age_death by Status
## F = 2.1365, num df = 29, denom df = 30, p-value = 0.0427
## alternative hypothesis: true ratio of variances is not equal to 1
## 95 percent confidence interval:
##  1.025839 4.470182
## sample estimates:
## ratio of variances 
##           2.136475
```

```r
# We don't have support to say that they have different variances, perfect, let's follow!

t.test(Age_death ~ Status, data = data, var.equal = T)
```

```
## 
## 	Two Sample t-test
## 
## data:  Age_death by Status
## t = 0.29654, df = 59, p-value = 0.7679
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -3.992605  5.381852
## sample estimates:
## mean in group Bipolar disorder  mean in group Healthy control 
##                       44.53333                       43.83871
```

1. What is the hypothesis?
    * $H_0: \mu_{Age_{Male}} = \mu_{Age_{Female}}$
    * $H_a: \mu_{Age_{Male}} \neq \mu_{Age_{Female}}$
1. What is the statistics?
    * Mean
1. What is the significance level?
    * 0.05
1. Results?
    * p-value = 0.7678611

**Interpretation**: The p-value is greater than 0.05, we cannot discard the null hypothesis, it means that we don't have enough support to say that the mean age is different among the two conditions.

#####  Test if two samples (with different variance) have the same mean

Two samples, with different variance: You want to know if 2 groups with different variance have the same mean.
* The samples, have to be independent.
* The groups can have different sizes.

Hypothesis:

$H_0: \mu_{1} = \mu_{2}$

$H_a: \mu_{1} \neq \mu_{2}$

* Example: Test if there was a difference in the duration of illness from individuals that took valproate therapy? Use the significance level of 0.05.


```r
## Again, we also must test for normality
shapiro.test(data$Duration_illness)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  data$Duration_illness
## W = 0.96852, p-value = 0.4995
```

```r
# It's normal, great. We have now to test for the variance in the two groups. 
var.test(data$Duration_illness ~ data$Therapy_Valproate, data = data)
```

```
## 
## 	F test to compare two variances
## 
## data:  data$Duration_illness by data$Therapy_Valproate
## F = 0.34472, num df = 19, denom df = 9, p-value = 0.04848
## alternative hypothesis: true ratio of variances is not equal to 1
## 95 percent confidence interval:
##  0.09358937 0.99281527
## sample estimates:
## ratio of variances 
##          0.3447213
```

```r
# We have support to say that they have different variances, perfect, let's follow!

t.test(data$Duration_illness ~ data$Therapy_Valproate, data = data, var.equal = F)
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  data$Duration_illness by data$Therapy_Valproate
## t = -1.2726, df = 12.198, p-value = 0.2269
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -15.441406   4.041406
## sample estimates:
##  mean in group No mean in group Yes 
##              18.6              24.3
```

1. What is the hypothesis?
    * $H_0: \mu_{Time Illness_{Valproate = yes}} = \mu_{Time Illness_{Valproate = no}}$
    * $H_a: \mu_{Time Illness_{Valproate = yes}} \neq \mu_{Time Illness_{Valproate = no}}$
2. What is the statistics?
    * Mean
3. What is the significance level?
    * 0.05
4. Results?
    * p-value = 0.226883

**Interpretation**: Once the p-value is greater than 0.05, we cannot discard the null hypothesis, it means that we don't have enough support to say that the average age of illness is different between the individuals that took valproate.

##### Test if one sample has the same mean in two different treatments

Paired samples: You want to know if the **same sample** has a difference between two treatments/time.
Hypothesis:

$H_0: \mu_{x, time 1} = \mu_{x, time 2}$

$H_a: \mu_{x, time 1} \neq \mu_{x, time 2}$


#### Testing median
If the samples **do not follow a Normal distribution**, we want to test if the median of the samples differs.

To test if the median of two medians is equal in two groups, we can use the **Wilcoxon-Mann-Whitney U test**. Sometimes, one may refer to the tests as only Mann-Whitney (independent samples) or Wilcoxon (paired). The Wilcoxon-Mann-Whitney test takes the ranks of each observation into account.

On R we can use the function `wilcox.test()`.

Hypothesis for Mann-Whitney:

$H_0: median_{1} = median_{2}$

$H_a: median_{1} \neq median_{2}$

Hypothesis for Wilcoxon:

$H_0: median_{time1} = median_{time2}$

$H_a: median_{time1} \neq median_{time1}$

* Example 1. We want to know if the average intake of Fluphenazine is different between suicidal. Use the significance level of 0.05.


```r
shapiro.test(data$Therapy_Fluphenazine)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  data$Therapy_Fluphenazine
## W = 0.46829, p-value = 3.696e-09
```

```r
wilcox.test(data$Therapy_Fluphenazine ~ data$Suicide, paired = F, exact = F)
```

```
## 
## 	Wilcoxon rank sum test with continuity correction
## 
## data:  data$Therapy_Fluphenazine by data$Suicide
## W = 124, p-value = 0.2605
## alternative hypothesis: true location shift is not equal to 0
```

1. What is the hypothesis?
    * $H_0: median_{Gene4} = median_{Gene4}$
    * $H_a: median_{Gene4} \neq median_{Gene4}$
2. What is the statistics?
    * Median
3. What is the significance level?
    * 0.05
4. Results?
    * p-value = 0.2604674

**Interpretation**: The p-value is greater than 0.05, we cannot discard the null hypothesis, it means that we do not have enough support to say that the median of intake of Fluphenazine is different in suicidal patients.

#### Testing proportion
When we are interested in the proportions of two different groups are different, we can use the proportion test.

**Important**: The proportional tests assume that the samples are unrelated.
On R we can call the function `prop.test()` for it.

Hypothesis:

$H_0: prop_{1} = prop_{2}$

$H_a: prop_{1} \neq prop_{2}$

* Example 1. We are interested in the Gender of our population. We want to know if we have the same proportion of males and females. For that, we can use the proportion test. Use the significance level of 0.05.


```r
prop.test(table(data$Gender))
```

```
## 
## 	1-sample proportions test with continuity correction
## 
## data:  table(data$Gender), null probability 0.5
## X-squared = 5.3115, df = 1, p-value = 0.02119
## alternative hypothesis: true p is not equal to 0.5
## 95 percent confidence interval:
##  0.2303951 0.4778092
## sample estimates:
##         p 
## 0.3442623
```
1. What is the hypothesis?
    * $H_0: prop_{F} = prop_{M}$
    * $H_a: prop_{F} \neq prop_{M}$
2. What is the statistics?
    * Proportion
3. What is my significance level?
    * 0.05
4. Outcome:
    * p-value = 0.0211854

**Interpretation**: The p-value was smaller than 0.05, so we discard the null hypothesis, meaning that we have enough support to say that we have different proportions for male and female in our study.

### Exercises
1. Test if the proportion of therapies differs on the patients.
1. Test if there is a difference on the (average or median) of brain pH and the status. And the brain side?
1. Test if the average expression of the genes differs by gender.
1. Test if the average expression of the genes differs is different in the controls.


### Association
When we are interested in the relationship between two or more variables, we are interested in its association. How we measure this association depends on the type of variables we are testing.

When we are interested in the relationship among two numerical variables, one can use a correlation. If we want between one categorical and one numerical, we can use ANOVA or a (general) linear model. When we have 2 variables that are categorical, we can use a Chi-squared test. 

#### Correlation
If we are interested in how two numerical variables are influencing on each other, we can use correlation. We can use linear (Pearson) correlation (it assumes that both variables are normally distributed). If at least one of the variables is not Normal, we should use Spearman Correlation.
Both correlations vary from -1 to 1, meaning that the closer to 0 it is the less correlated the variables are, and the closest to the extremes, the more correlated it is. Having a correlation of +1 means that they are perfectly positively correlated (if one goes up, the other will go up on the same proportion, or if one goes down, the other will go down in the same proportion). Having a correlation of -1 means that they are perfectly negatively correlated (if one goes up, the other will go down on the same proportion, or if one goes down, the other will go up in the same proportion).
To compute the correlation on R, we can use the function `cor(x, y, method = "spearman")` for Spearman correlation or `cor(x, y, method = "pearson")` for Pearson correlation.

* Example 1. What is the correlation between the expression values for SOX9 and EFEMP1?


```r
plot(data$SOX9, data$EFEMP1, 
     xlab= "SOX9",
     ylab = "EFEMP1",
     pch = 16,
     las = 1)
```

![](Material_BD_files/figure-html/unnamed-chunk-56-1.png)<!-- -->

```r
cor(data$SOX9, data$EFEMP1)
```

```
## [1] 0.8451555
```

```r
cor(data$SOX9, data$EFEMP1, method = 's')
```

```
## [1] 0.8231095
```

**Interpretation**: The correlation of the expression between the genes SOX9 and EFEMP1 is `0.8451555`, it means that the gene expression of both genes increases at the same time.


* Example 2. How is the correlation between the expression values for GJA1 and DUSP4?


```r
cor(data$GJA1, data$DUSP4, method = "p")
```

```
## [1] -0.09835933
```

```r
cor(data$GJA1, data$DUSP4, method = "s")
```

```
## [1] -0.1555791
```

```r
plot(data$GJA1, data$DUSP4, 
     xlab = "GJA1",
     ylab = "DUSP4",
     las = 1,
     pch = 16)
```

![](Material_BD_files/figure-html/unnamed-chunk-57-1.png)<!-- -->

**Interpretation**: The Spearman correlation between the genes GJA1 and DUSP4 is `-0.1555791`, it means that the correlation is really small.

#### Contingency tables
When we are interested in how two categorical variables are related to each other, we can use Fisher's exact test or Chi-Squared. What will make you choose your test is basically the limitations. The Chi-Squared test is less conservative than the Fisher test.

Limitations for the Chi-Squared test usage:

* Big samples will always return significance, even when it doesn't exist;
* It's sensitive to small samples. If you have less than 20% in one cell or less than 5 samples, it's better to use a Fisher test.

Hypothesis:

* $H_0$: Variable A and Variable B are independent.
* $H_a$: Variable A and Variable B are not independent.
The functions `fisher.test()` and `chisq.test()` compute this tests on R.

* Example 1. Test if there an association of the Gender and having the disorder. Consider a confidence level of 95%.


```r
table(data$Gender, data$Status)
```

```
##         
##          Bipolar disorder Healthy control
##   Female               14               7
##   Male                 16              24
```


```r
table(data$Gender, data$Status) %>%
fisher.test(.)
```

```
## 
## 	Fisher's Exact Test for Count Data
## 
## data:  .
## p-value = 0.062
## alternative hypothesis: true odds ratio is not equal to 1
## 95 percent confidence interval:
##   0.878418 10.712172
## sample estimates:
## odds ratio 
##   2.944493
```


```r
table(data$Gender, data$Status) %>%
chisq.test(.)
```

```
## 
## 	Pearson's Chi-squared test with Yates' continuity correction
## 
## data:  .
## X-squared = 2.9237, df = 1, p-value = 0.08729
```

**Interpretation**: We don't have support to reject the null hypothesis, it means, we cannot reject the hypothesis that having Bipolar Disorder is a gender-related disorder.


### ANOVA
The ANOVA basically splits the variability in variability inside the groups and variability between groups and compares both. The bigger the variability between the groups, the bigger is the evidence that there is a difference between the groups, it means: They have different means!

Remember! If we want to test means the variable must follow a Normal distribution!

Hypothesis:

 * $H_0$: The means are the same in all treatments.
 * $H_a$: The means differ in at least one treatment.
To use ANOVA on R we can use the function `aov()` or `lm()`.

Example 1: Check if there is a difference in the gene expression of SOX9 depending on levels of alcohol abuse.


```r
data$Alcohol_abuse %<>% as.factor()


shapiro.test(data$SOX9)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  data$SOX9
## W = 0.96446, p-value = 0.07367
```

```r
# Yep! Let's do the anova now.
fit1 <- aov(data$SOX9 ~ data$Alcohol_abuse)
fit1
```

```
## Call:
##    aov(formula = data$SOX9 ~ data$Alcohol_abuse)
## 
## Terms:
##                 data$Alcohol_abuse Residuals
## Sum of Squares           0.0107959 0.5215027
## Deg. of Freedom                  5        54
## 
## Residual standard error: 0.09827236
## Estimated effects may be unbalanced
## 1 observation deleted due to missingness
```

```r
# Let's check the results
summary(fit1)
```

```
##                    Df Sum Sq  Mean Sq F value Pr(>F)
## data$Alcohol_abuse  5 0.0108 0.002159   0.224  0.951
## Residuals          54 0.5215 0.009657               
## 1 observation deleted due to missingness
```

```r
# A way to see if it makes sense is to plot the boxplot for each level.
boxplot(data$SOX9 ~ data$Alcohol_abuse, 
        xlab = "Alcohol abuse", 
        ylab = "SOX9", las = 1)
```

![](Material_BD_files/figure-html/unnamed-chunk-61-1.png)<!-- -->

##### Look at Diagnostic Plots
**We have to check if our model is well adjusted to our data.**

* Basically:

    * Residuals should be randomly distributed around 0.
    * The residuals should follow a Normal distribution.
    * Cook's distance should be smaller than 10. (Indicates outliers)
    * Who has to adapt to the data is the model, never the opposite


```r
layout(matrix(c(1,2,3,4),2,2)) # optional layout
plot(fit1, pch = 16, las = 1) # diagnostic plots
```

![](Material_BD_files/figure-html/unnamed-chunk-62-1.png)<!-- -->

1. What is the hypothesis?
    * $H_0: \mu_{alchool = 0} = \mu_{alchool = 1} = ... = \mu_{alchool = 5}$
    * $H_a: \mu_{alchool = 0} \neq \mu_{alchool = 1} \neq ... \neq \mu_{alchool = 5}$
1. What is the statistics?
    * Mean
2. What is the significance level?
    * 0.05
3. Results?
    * p-value > 0.05
**Interpretation**: The p-value is greater than 0.05, so we have no support to reject the null hypothesis and say that there is an association between Alcohol abuse and the expression levels of SOX9.


##### Multiple Comparisons
In the case where at least one mean differs from the other(s), How do we know which mean is different?

One option is to use the **Tukey's Method** to test all possible pairwise differences of means to determine if at least one difference is significantly different from 0;
On R the function is ``TukeyHSD(aov())``.

Another option is **Scheffé's Method** to test all possible contrasts at the same time, to see if at least one is significantly different from 0;
On R, ``scheffe.test(aov, "treatment")``.

Another option is **Duncan**. It is much more conservative, and more powerful than the other, but it doesn't control the error rate correctly;
On R, ``duncan.test(aov, "treatment")``.

```r
# Tukey Honestly Significant Differences
TukeyHSD(fit1)
```

```
##   Tukey multiple comparisons of means
##     95% family-wise confidence level
## 
## Fit: aov(formula = data$SOX9 ~ data$Alcohol_abuse)
## 
## $`data$Alcohol_abuse`
##             diff         lwr       upr     p adj
## 1-0  0.025853063 -0.07266358 0.1243697 0.9705417
## 2-0 -0.002588266 -0.14852217 0.1433456 0.9999999
## 3-0 -0.004145659 -0.15007957 0.1417882 0.9999994
## 4-0  0.030100128 -0.09827274 0.1584730 0.9820127
## 5-0  0.018463549 -0.10390575 0.1408328 0.9976591
## 2-1 -0.028441329 -0.17719820 0.1203155 0.9928779
## 3-1 -0.029998722 -0.17875560 0.1187582 0.9908963
## 4-1  0.004247066 -0.12732609 0.1358202 0.9999988
## 5-1 -0.007389514 -0.13311202 0.1183330 0.9999768
## 3-2 -0.001557393 -0.18518686 0.1820721 1.0000000
## 4-2  0.032688394 -0.13731946 0.2026962 0.9926896
## 5-2  0.021051815 -0.14446955 0.1865732 0.9989690
## 4-3  0.034245788 -0.13576207 0.2042536 0.9909430
## 5-3  0.022609208 -0.14291216 0.1881306 0.9985458
## 5-4 -0.011636580 -0.16190371 0.1386306 0.9999090
```

```r
plot(TukeyHSD(fit1), las=1)
```

![](Material_BD_files/figure-html/unnamed-chunk-63-1.png)<!-- -->

### Non-parametric ANOVA
#### Kruskall Wallis
It is a one-way ANOVA, an extension of the Mann-Whitney test. It is used for **independent samples**.

To use this test in R we can use the function ``kruskal.test()``.

Example: Test if the levels of the gene expression of the gene is different between case and controls.


```r
shapiro.test(data$SST)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  data$SST
## W = 0.90416, p-value = 0.0001656
```

```r
kruskal.test(data$SST~data$Status)
```

```
## 
## 	Kruskal-Wallis rank sum test
## 
## data:  data$SST by data$Status
## Kruskal-Wallis chi-squared = 17.023, df = 1, p-value = 3.693e-05
```

```r
boxplot(data$SST~data$Status, las = 1,
        col = c('coral','turquoise'))
```

![](Material_BD_files/figure-html/unnamed-chunk-64-1.png)<!-- -->

#### Friedmann
It is a one-way ANOVA, an extension of the Wilcoxon test. It is used for **dependent samples**. You must have complete cases, it means, all groups have to have the same size.
To use this test in R we can use the function ``friedman.test()``.


### Regression Models
### Linear Regression
Linear regression is an approach for modelling the linear relationship between a response variable Y and one or more explanatory variables (or independent variables) denoted by X.

There are a lot of assumptions to use this methodology:

* We assume that the response variable is error free (has no measurement error);
* We assume that the explanatory we use are independents - multicolinerarity;
* We assume that the variance is constant;
* We assume that the errors are independent;
* This is the mathematical expression of a regression model: $Y = \beta_{0} + \beta_{1}\times{X_1 }+ ... + \beta_{j}\times{X_j} + \epsilon$

**Interpretation**: A fitted linear regression model can be used to identify the relationship between a single explanatory variable $X_j$ and the response variable $Y$ when all the other explanatory variables in the model are "held fixed". Specifically, the interpretation of $\beta_j$ is the expected change in Y for a one-unit change in $X_j$ when the other covariates are held fixed.

If we are using the linear model, we assume that the response variable is normally distributed. If we have other distributions, we can use a general linear model that accounts for other models as the Gamma, the Beta, the Poisson etc.

In all of them, we should always pay attention to the residuals, that should follow a Normal distribution, and the way to check it is similar to the ANOVA.

On R we can use the function `glm()` for modelling, the family parameter gives you the kind of regression you are doing. When a normal distribution is assumed, the function `lm()` can be used.

**Important**: The way to interpret each kind of family changes. Some are in percentage, some are in odds ratio, some are linear. For that, you should find more information before using it.

* Example 1: Suppose we are interested in how much, on average, the gene expression of one gene alters with the time after death.


```r
cor(data$Post_morten_interval, data$CLDN10)
```

```
## [1] -0.3810924
```

```r
plot(data$Post_morten_interval, data$CLDN10, pch = 20)
```

![](Material_BD_files/figure-html/unnamed-chunk-65-1.png)<!-- -->


```r
shapiro.test(data$CLDN10)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  data$CLDN10
## W = 0.9634, p-value = 0.06516
```

```r
mod <- lm(data$CLDN10 ~ data$Post_morten_interval)
summary(mod)
```

```
## 
## Call:
## lm(formula = data$CLDN10 ~ data$Post_morten_interval)
## 
## Residuals:
##       Min        1Q    Median        3Q       Max 
## -0.185611 -0.053120  0.000983  0.065988  0.149693 
## 
## Coefficients:
##                            Estimate Std. Error t value Pr(>|t|)    
## (Intercept)                2.113098   0.025012  84.484  < 2e-16 ***
## data$Post_morten_interval -0.002162   0.000683  -3.166  0.00244 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.08416 on 59 degrees of freedom
## Multiple R-squared:  0.1452,	Adjusted R-squared:  0.1307 
## F-statistic: 10.02 on 1 and 59 DF,  p-value: 0.002445
```

```r
fit <- glm(data$CLDN10 ~ data$Post_morten_interval, family = "gaussian")
summary(fit)
```

```
## 
## Call:
## glm(formula = data$CLDN10 ~ data$Post_morten_interval, family = "gaussian")
## 
## Deviance Residuals: 
##       Min         1Q     Median         3Q        Max  
## -0.185611  -0.053120   0.000983   0.065988   0.149693  
## 
## Coefficients:
##                            Estimate Std. Error t value Pr(>|t|)    
## (Intercept)                2.113098   0.025012  84.484  < 2e-16 ***
## data$Post_morten_interval -0.002162   0.000683  -3.166  0.00244 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for gaussian family taken to be 0.007083715)
## 
##     Null deviance: 0.48895  on 60  degrees of freedom
## Residual deviance: 0.41794  on 59  degrees of freedom
## AIC: -124.87
## 
## Number of Fisher Scoring iterations: 2
```

After fitting a model we have to check its residuals.


```r
shapiro.test(fit$residuals)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  fit$residuals
## W = 0.97385, p-value = 0.2156
```

```r
par(mfrow = c(2,2))

plot(mod, which = c(1:4), add.smooth = FALSE, pch=20, las = 1)
```

![](Material_BD_files/figure-html/unnamed-chunk-67-1.png)<!-- -->


$\beta_0$ - It is the value predicted for the null effect of the CLDN10 gene expression, in this case, this value is 2.11. It means that, on average, the mean expression of CLDN10 is 2.11, when the post-Morten interval is 0.

$\beta_1$ - It is the diminution (negative value) in the predicted CLDN10 gene expression for the increase on 1 unity on the Post Morten Interval, In this case, the diminution predicted on the gene expression, is of -0.002 for each 1 unity increased on the post-Morten interval.

Question: Does it make sense that the gene expression decreases with the increase of the time?


### Exercises
1. Is there a correlation between the levels of APOLD1 and TAC1?
1. Test if there is an association of Suicide and Patients treated with lithium. And for patients treated with valproate?
1. Test if there is a difference on the gene expression of the genes CLDN10, EFEMP1, PLSCR4, SOX9 in cases and controls. And male and females? Consider a model with gender and disease status. 
Do not forget to make the diagnosis of the models. 

## Multivariate Analysis
The multivariate analysis aims to reduce the dimensionality of datasets with more than one response variable.

### Principal Component Analysis (PCA)
Principal component analysis (PCA) is a statistical procedure that transforms a set of possibly correlated variables into a set of values of linearly uncorrelated variables, the principal components (PC). The number of PC is less or equal to the number of variables. The first PC has the largest variance and it decreases.

PCA is sensible to the scale of variables. So it needs to normalize the data before running it.

The PCA can be called on R using the functions: `prcomp()` and `princomp()`.

* Example. Using only the genes in our dataset, let's see if there is any cluster. Later, plot it and colour by gender and disorder.


```r
pca = prcomp(t(data[,-c(1:16)]), scale. = T, center = T)
plot(pca)
```

![](Material_BD_files/figure-html/unnamed-chunk-68-1.png)<!-- -->

```r
plot(pca$rotation, col = c('red', 'blue')[unclass(data$Status)], pch = 16, las = 1)
legend('bottomleft', 
       levels(data$Status), 
       col = c('red', 'blue'), 
       pch = 16, 
       bty = 'n')
```

![](Material_BD_files/figure-html/unnamed-chunk-68-2.png)<!-- -->

```r
plot(pca$rotation, col = c('red', 'blue')[unclass(data$Status)], pch = c(4, 16)[unclass(data$Gender)], las = 1)

legend('bottomleft', 
       c(levels(data$Status), levels(data$Gender)), 
       col = c('red', 'blue', 'black', 'black'), 
       pch = c(15,15,4,16), 
       bty = 'n')
```

![](Material_BD_files/figure-html/unnamed-chunk-68-3.png)<!-- -->

```r
require(scatterplot3d)
```

```
## Loading required package: scatterplot3d
```

```r
scatterplot3d::scatterplot3d(pca$rotation[,1], 
                             pca$rotation[,3],
                             pca$rotation[,2],
                             xlab = 'PCA1', 
                             ylab = 'PCA3', 
                             zlab = 'PCA2', 
                             las = 1, 
                             color = c('red', 'blue')[unclass(data$Status)], 
                             pch = c(4, 16)[unclass(data$Gender)] )
```

![](Material_BD_files/figure-html/unnamed-chunk-68-4.png)<!-- -->


