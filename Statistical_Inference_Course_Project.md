---
title: "Statistical_inference_Course_project"
author: "Himanshu Raj"
date: "9 May 2018"
output: 
  html_document:
    keep_md: true
---


### Project Part 1: A simulation exercise

#### Overview

In this project we will investigate the exponential distribution in R and compare it with the Central Limit Theorem. The exponential distribution can be simulated in R with rexp(n, lambda) where lambda is the rate parameter.

#### Starting with the Simulation
The mean of the Exponential distribution is $\frac{1}{\lambda}$ and the standard deviation is also $\frac{1}{\lambda}$. We will investigate the distribution of averages of 40 exponentials.
T The exponential distribution can be simulated in R with `rexp(n, lambda)` where lambda is the rate parameter. We are setting `lambda = 0.2` for all of the simulations.

#### Loading Libraries


```r
library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.4.4
```

Initializing the simulation controlling variables.

```r
noSim <- 1000
sampSize <- 40
lambda <- 0.2
```

Setting the seed of the Random Number Generator and creating matrix for 1000 rows and 40 samples

```r
set.seed(123)
simMatrix <- matrix(rexp(n = noSim * sampSize, rate = lambda), noSim, sampSize)
```

Creating a vector of thousand rows containing the mean of each row of the simMatrix and combining into dataframe

```r
simMean <- rowMeans(simMatrix)
simData <- data.frame(cbind(simMatrix, simMean))
dim(simData)
```

```
## [1] 1000   41
```

### Plotting and Visualising the data

```r
ggplot(data = simData, aes(simData$simMean)) + 
  geom_histogram(breaks = seq(2, 9, by = 0.2), col = "blue", aes(fill = ..count..)) + 
  labs(title = "Histogram of Mean Distribution", x = "Simulation Means", y = "Frequency") + 
  geom_vline(aes(xintercept=mean(simData$simMean)), color="red", 
             linetype="dashed", size=1)
```

![](Statistical_Inference_Course_Project_files/figure-html/plot-1.png)<!-- -->

### Comparing Sample mean to the theoretical mean of the distribution.


```r
actualMean <- mean(simMean)
theoreticalMean <- (1 / lambda)
```

The actual mean of the simulated mean sample data is 5.0119113 and the theoretical mean is 5.

Thus, we can see that the actual mean of the simulated mean sample data is very close to the theoretical mean of original data distribution.

### Comparing the sample variance and the theoretical variance of the distribution.


```r
actualVariance <- var(simMean)
theoreticalVariance <- ((1 / lambda) ^ 2) / sampSize
```

The actual variance of the simulated mean sample data is 0.6088292 and the  theoretical variance is 0.625.

Thus, we can see that the actual variance of the simulated mean sample data is very close to the theoretical variance of original data distribution.

### Observing the distribution

To prove that the simulated mean sample data approximately follows the Normal distribution, we perform the following three steps:

Step 1: Create an approximate normal distribution and see how the sample data alligns with it.


```r
qplot(simMean, geom = 'blank') + 
  geom_line(aes(y=..density.., colour='Empirical'), stat='density', size=1) + 
  stat_function(fun=dnorm, args=list(mean=(1/lambda), sd=((1/lambda)/sqrt(sampSize))), 
                aes(colour='Normal'), size=1) + 
  geom_histogram(aes(y=..density.., fill=..density..), alpha=0.4, 
                 breaks = seq(2, 9, by = 0.2), col='red') + 
  scale_fill_gradient("Density", low = "green", high = "blue") + 
  scale_color_manual(name='Density', values=c('brown', 'blue')) + 
  theme(legend.position = c(0.85, 0.60)) + 
  labs(title = "Mean Density Distribution", x = "Simulation Means", y = "Density")
```

![](Statistical_Inference_Course_Project_files/figure-html/Distrib-1.png)<!-- -->
  
From above histogram, the simulated mean sample data can be adequately approximated with the normal distribution.

Step 2: Compare the 95% confidence intervals of the simulated mean sample data and the theoretical normally distributed data.


```r
actualConfInterval <- actualMean+c(-1,1)*1.96*sqrt(actualVariance)/sqrt(sampSize)
theoreticalConfInterval <- theoreticalMean+c(-1,1)*1.96*
  sqrt(theoreticalVariance)/sqrt(sampSize)
```

Actual 95% confidence interval is [4.7414712, 5.2317681] and Theoretical 95% confidence interval is [4.755, 5.245] and we see that both of them are approximately same.

Step 3: q-q Plot for Qunatiles.

```r
qqnorm(simMean)
qqline(simMean)
```

![](Statistical_Inference_Course_Project_files/figure-html/Distrib3-1.png)<!-- -->

The actual quantiles also closely match the theoretical quantiles, hence the above three steps prove that the distribution is approximately normal.
