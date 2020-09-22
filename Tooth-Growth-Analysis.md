---
title: "Tooth Growth Analysis"
author: "Raymond Dineen"
date: "9/22/2020"
output: 
    pdf_document:
        keep_md: true
---

## Part 2:

### Overview

The ToothGrowth dataset in R shows data from a test that measured the growth of teeth in guinea pigs after being given varying doses of vitamin C (0,5, 1, and 2 mg/day) by two different delivery methods, orange juice (OJ) and ascorbic acid (VC). We will be looking at whether dosage and delivery method affect tooth growth. We can start with a simple boxplot of data, looking at length based on both delivery method and dose. We can see a general trend but not anything strong enough to come to any immediate conclusions.

Figure 3:


```r
data("ToothGrowth")
boxplot(len ~ supp + dose, data = ToothGrowth)
```

![](Tooth-Growth-Analysis_files/figure-latex/unnamed-chunk-1-1.pdf)<!-- --> 

### Does delivery method effect tooth growth?

A good place to start is to see if delivery method effects tooth growth regardless of dose. We can use a t-test to get a p-value. Our null hypothesis is that there is no difference between the two delivery methods. Our alternative hypothesis will be that the OJ results in greater length than VC because of the trend we observed in the boxplot we just made.


```r
data("ToothGrowth")
grpOJ <- ToothGrowth$len[ToothGrowth$supp == "OJ"]
grpVC <- ToothGrowth$len[ToothGrowth$supp == "VC"]
t.test(grpOJ, grpVC, alt = "greater")$p.value
```

```
## [1] 0.03031725
```

Our pvalue is less than 0.05 which leads us to reject the null hypothesis. We can also back up our conclusion with a permutation test which randomly reassigns delivery methods to lengths and see how many end up giving us a more extreme result than the actual result. 


```r
lengths <- ToothGrowth$len
supps <- ToothGrowth$supp
testStat <- function(len, method) mean(len[method == "OJ"]) - mean(len[method == "VC"])
observedStat <- testStat(lengths, supps)
set.seed(1)
permutations <- sapply(1:10000, function(i) testStat(lengths, sample(supps)))
mean(permutations > observedStat)
```

```
## [1] 0.0311
```

As we can see from the test, roughly 3% of permutations resulted in a higher difference in means than the observed permutation. This result is very similar to our p-value and further leads us to reject the null hypothesis.

### Does dose affect tooth growth?

Our boxplot leads us to believe that higher doses results in greater tooth length. We have more than two groups to compare so we will have to do a few different t-tests to try to get the full picture. We can start with the what seems to have the most obvious conclusion. We test the alternative hypothesis that 2 mg/day results in greater growth than 0.5 mg/day.


```r
grp2 <- ToothGrowth$len[ToothGrowth$dose == 2]
grp0.5 <- ToothGrowth$len[ToothGrowth$dose == 0.5]
t.test(grp2, grp0.5, alt = "greater")$p.value
```

```
## [1] 2.198762e-14
```

The p-value for this test is extremely low and we can confidently reject the null hypothesis. Next we want to check if 2 mg/day results in more growth than 1 mg/day. 


```r
grp1  <- ToothGrowth$len[ToothGrowth$dose == 1]
t.test(grp2, grp1, alt = "greater")$p.value
```

```
## [1] 9.532148e-06
```

Again we see another extremely low p-value which leads us to reject the null hypothesis.

### Conclusions

From our analysis we can confidently say that higher doses of vitamin C result in greater tooth growth in guinea pigs tested. We are also fairly confident from our t-test and permutation test that orange juice was a better delivery method than ascorbic acid. We assumed that the data was t-distributed within each unique combination of dose and delivery method.
