---
layout: default
title: Hypothesis testing
nav_order: 2
---

# Hypothesis testing
{: .no_toc }


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

A statistical hypothesis is an assumption about a population parameter. This assumption may or may not be true.
In traditional / frequentist Statistics there are two different statistical hypotheses:

* **Null hypothesis**: denoted by $H_0$ or H0, is usually the hypothesis that sample observations result **purely from chance**. In general, it is an equality assumption.

* **Alternative hypothesis**: denoted by $H_1$ or $H_a$, is the hypothesis that sample observations are influenced by some non-random cause. In general, it is inequality, superiority or inferiority.

There are two outcomes from Hypothesis testing:

1. **you reject the null hypothesis**;
1. **you fail to reject the null hypothesis**. We never say that we accepted the null hypothesis. Failure to reject means that the data are not sufficiently persuasive for us to prefer the alternative hypothesis over the null hypothesis.

### Steps to test a null hypothesis

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

### Type I and type II errors
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


