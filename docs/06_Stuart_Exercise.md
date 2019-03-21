---
layout: default
title: Exercise Results
nav_order: 2
mathjax: true
---

# Exercise Results
{: .no_toc }


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## One of the possible solutions to Stuart Baird exercise.

Plot the Max Log Likelihood for all locus.

```r
data= read.csv("~/Downloads/StuartsReads.csv", header=F, row.names=1, stringsAsFactors = F)
require(magrittr)
data[1:5,1:5]

### Fours Steps

# 1st: form hap.: Write a function ToSortedDiplotypes -> 
# Save into SortedDiplotypes

ToSortedDiplotypes <- function(G1, G2){
  G3 = rbind(G1,G2)
  Min = apply(G3, 2, min)
  Max = apply(G3, 2, max)
  G4_aux = data.frame(Min,Max)
  G4 = apply(G4_aux, 1, function(x) paste0(x[1], x[2]))
  return(G4)
}

ToSortedDiplotypes(data[1,],data[2,])

IDS = seq(from = 1, to = (nrow(data)-1), by = 2)

for (rows in IDS){
  print (rows)
  Out = ToSortedDiplotypes(data[rows,], data[rows+1,])  
  if(rows > 1){
    M = rbind(M,Out)
  } 
  if (rows == 1){
    M = Out
  }
}
SortedDiplotypes =M

# for ( i in 1:ncol(SortedDiplotypes)){
for ( i in 1:10){
  TAB = table(SortedDiplotypes[,i])
  NAs = stringr::str_count(names(TAB), '_')
  TAB = TAB[NAs == 0]
  TAB %<>% as.data.frame(.)
  row.names(TAB) = TAB$Var1
  names(TAB) = c('Allele', paste0('A_',i))
  # TAB = TAB[-1]
  if(i == 1){
    SortedDiplotypesTallies = TAB
  }
  if (i > 1){
    SortedDiplotypesTallies = plyr::join( SortedDiplotypesTallies,TAB, type = 'full')
  }
}
SortedDiplotypesTallies[,1:5]
```
