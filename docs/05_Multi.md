---
layout: default
title: Multivariate Analysis
nav_order: 2
mathjax: true
---

# Multivariate Analysis
{: .no_toc }


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
The multivariate analysis aims to reduce the dimensionality of datasets with more than one response variable.

## Principal Component Analysis (PCA)
Principal component analysis (PCA) is a statistical procedure that transforms a set of possibly correlated variables into a set of values of linearly uncorrelated variables, the principal components (PC). The number of PC is less or equal to the number of variables. The first PC has the largest variance and it decreases.

PCA is sensitive to the scale of variables. So it needs to normalize the data before running it.

The PCA can be called on R using the functions: `prcomp()` and `princomp()`.

* Example. Using only the genes in our dataset, let's see if there is any cluster. Later, plot it and colour by gender and disorder.



```r
pca = prcomp(t(data[,-c(1:16)]), scale. = T, center = T)
plot(pca)
```

![](figure-html/unnamed-chunk-102-1.png)<!-- -->



```r
plot(pca$rotation, col = c('red', 'blue')[unclass(data$Status)], pch = 16, las = 1)
legend('bottomleft', 
       levels(data$Status), 
       col = c('red', 'blue'), 
       pch = 16, 
       bty = 'n')
```

![](figure-html/unnamed-chunk-103-1.png)<!-- -->


```r
plot(pca$rotation, col = c('red', 'blue')[unclass(data$Status)], pch = c(4, 16)[unclass(data$Gender)], las = 1)

legend('bottomleft', 
       c(levels(data$Status), levels(data$Gender)), 
       col = c('red', 'blue', 'black', 'black'), 
       pch = c(15,15,4,16), 
       bty = 'n')
```

![](figure-html/unnamed-chunk-104-1.png)<!-- -->



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

![](figure-html/unnamed-chunk-106-1.png)<!-- -->


