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
data = read.csv('~/StuartsReads.csv', header = F,
                row.names = 1,
                stringsAsFactors = F)
data[1:5,1:5]
require(magrittr)
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
SortedDiplotypes = M

for ( i in 1:ncol(SortedDiplotypes)){
  # for ( i in 1:10){
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


### Step 2
for ( i in 1:ncol(data)){
  data2 = data
  data[data == '_'] <- NA
  TAB = table(data[,i]) %>% prop.table()
  # TAB
  TAB %<>% as.data.frame(.)
  row.names(TAB) = TAB$Var1
  names(TAB) = c('Allele', paste0('A_',i))
  # TAB = TAB[-1]
  if(i == 1){
    StateFrequencies = TAB
  }
  if (i > 1){
    StateFrequencies = plyr::join( StateFrequencies,TAB, type = 'full')
  }
}
row.names(StateFrequencies) = StateFrequencies$Allele
StateFrequencies = StateFrequencies[,-1]
StateFrequencies = StateFrequencies[order(row.names(StateFrequencies)),]

row.names(SortedDiplotypesTallies) = SortedDiplotypesTallies$Allele
SortedDiplotypesTallies = SortedDiplotypesTallies[,-1]
SortedDiplotypesTallies = SortedDiplotypesTallies[order(row.names(SortedDiplotypesTallies)),]

LLforF <- function(p,q, F., P, Q, PQ){
  LL  = P*log(p^2+F.) + Q*log(q^2+F.) + PQ*log(2*p*q* (1-F.))
  return(LL)
}

F.vec = seq(from = 0.01, to = 0.99, by = 0.01)
OUT = matrix(NA, ncol = 3)
for (i in 1:ncol(SortedDiplotypesTallies)){
  for(j in 1:length(F.vec)){
    SortedDiplotypesTallies_aux = SortedDiplotypesTallies[i] %>% na.exclude()
    StateFrequencies_aux = StateFrequencies[i]%>% na.exclude()
    LL = LLforF(p = StateFrequencies_aux[1,1],
                q = StateFrequencies_aux[2,1],
                P = SortedDiplotypesTallies_aux[1,1],
                PQ = SortedDiplotypesTallies_aux[2,1],
                Q = SortedDiplotypesTallies_aux[3,1], F. = F.vec[j] )
    OUT_aux = cbind(i, LL, F.vec[j])
    OUT = rbind(OUT, OUT_aux)
  }
}
dim(OUT )

OUT = data.frame(OUT)
OUT = OUT[-1, ]
head(OUT)
names(OUT) = c('Col', 'LL', 'F')
LL180 = subset(OUT, OUT$Col == 180)
LL180ML = subset(OUT, OUT$LL == max(LL180$LL))

plot(LL180$F,LL180$LL , type = 'l', xlab = 'F', ylab = 'LL', las = 1)
abline(h = max(LL180$LL))
abline(v = max(LL180ML$F))

MLE_SUP = matrix(NA, ncol = 3)
for (i in 1:nrow(SortedDiplotypes)){
  Xx = subset(OUT, OUT$Col == i)
  XxML = subset(OUT, OUT$LL == max(Xx$LL))
  
  Xx_sup = subset(Xx, Xx$LL >= (XxML$LL-2))
  LB = min(Xx_sup$F)
  UB = max(Xx_sup$F)
  MLF = XxML$F
  MLE_SUP_aux = cbind(LB, UB, MLF)
  MLE_SUP = rbind(MLE_SUP, MLE_SUP_aux)
}
MLE_SUP %<>% as.data.frame() %>% na.exclude()
dim(MLE_SUP)
plot(MLE_SUP$MLF, type = 'l', las = 1,
     ylab = 'MLE')
lines(MLE_SUP$LB, col = 'green')
lines(MLE_SUP$UB, col = 'red')
```
