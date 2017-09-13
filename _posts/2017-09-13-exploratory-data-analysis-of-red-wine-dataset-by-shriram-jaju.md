---
layout: post
published: false
title: Exploratory Data Analysis of Red Wine Dataset by Shriram Jaju
---
Exploratory Data Analysis of Red Wine Dataset by Shriram Jaju
========================================================

In this analysis I will be exploring Red Wine dataset.

This dataset is public available for research. The details are described in 
[Cortez et al., 2009]. 
Please include this citation if you plan to use this database:

  P. Cortez, A. Cerdeira, F. Almeida, T. Matos and J. Reis. 
  Modeling wine preferences by data mining from physicochemical properties.
  In Decision Support Systems, Elsevier, 47(4):547-553. ISSN: 0167-9236.

Available at: 
[@Elsevier] http://dx.doi.org/10.1016/j.dss.2009.05.016
[Pre-press (pdf)] http://www3.dsi.uminho.pt/pcortez/winequality09.pdf
[bib] http://www3.dsi.uminho.pt/pcortez/dss09.bib

1. Title: Wine Quality 

2. Sources:
Created by: Paulo Cortez (Univ. Minho), Antonio Cerdeira, Fernando Almeida,
Telmo Matos and Jose Reis (CVRVV) @ 2009
   
3. Attribute information:

For more information, read [Cortez et al., 2009].

Input variables (based on physicochemical tests):
1. fixed acidity (tartaric acid - g / dm^3)
2. volatile acidity (acetic acid - g / dm^3)
3. citric acid (g / dm^3)
4. residual sugar (g / dm^3)
5. chlorides (sodium chloride - g / dm^3
6. free sulfur dioxide (mg / dm^3)
7. total sulfur dioxide (mg / dm^3)
8. density (g / cm^3)
9. pH
10. sulphates (potassium sulphate - g / dm3)
11. alcohol (% by volume)
Output variable (based on sensory data): 
12. quality (score between 0 and 10)


```{r echo=FALSE, message=FALSE, warning=FALSE, packages}
# Load all of the packages that you end up using in your analysis in this code
# chunk.

# Notice that the parameter "echo" was set to FALSE for this code chunk. This
# prevents the code from displaying in the knitted HTML output. You should set
# echo=FALSE for all code chunks in your file, unless it makes sense for your
# report to show the code that generated a particular plot.

# The other parameters for "message" and "warning" should also be set to FALSE
# for other code chunks once you have verified that each plot comes out as you
# want it to. This will clean up the flow of your report.

library(ggplot2)
library(grid)
library(gridExtra)
library(dplyr)
library(RColorBrewer)
library(GGally)
```


```{r echo=FALSE, message=FALSE, warning=FALSE}
# Load the Data
wine <- read.csv("wineQualityReds.csv")
redwine <- subset(wine, select = -X)
dim(redwine)
```

```{r echo=FALSE, message=FALSE, warning=FALSE}
str(redwine)
```

Quality will be the main focus of this analysis. I want to know which variables
can best affect the quality of wine, and what are their optimal quantities.
This dataset consists of 12 variables and 1599 observations/wines, so I hope 
there is much to be learned.


```{r echo=FALSE, message=FALSE, warning=FALSE}
summary(redwine)
```


```{r echo=FALSE, message=FALSE, warning=FALSE}
redwine$quality.factor <- factor(redwine$quality, ordered = T)
```


# Univariate Plots Section


```{r echo=FALSE, message=FALSE, warning=FALSE}
ggplot(data = redwine, aes(x = quality.factor, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(stat = "count")

```

We see that wine quality is approximately normally distributed.

```{r echo=FALSE, message=FALSE, warning=FALSE}

grid.arrange(ggplot(data = redwine, aes( x = 1, y = pH ) ) + 
               geom_jitter(alpha = 0.2 ) +
               geom_boxplot(alpha = 0.2, color = 'red' ) ,
             ggplot(data = redwine, aes(x = pH, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(), ncol=2)
```

The pH is normally distributed, with few outliers.


```{r echo=FALSE, message=FALSE, warning=FALSE}

grid.arrange(ggplot(data = redwine, aes( x = 1, y = fixed.acidity ) ) + 
               geom_jitter(alpha = 0.2 ) +
               geom_boxplot(alpha = 0.2, color = 'red' ) ,
             ggplot(data = redwine, aes(fixed.acidity, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(), ncol=2)
```

Fixed acidity have some outliers and peaks between 6 and 7 g/dm^3.

```{r echo=FALSE, message=FALSE, warning=FALSE}
grid.arrange(ggplot(data = redwine, aes( x = 1, y = volatile.acidity ) ) + 
               geom_jitter(alpha = 0.2 ) +
               geom_boxplot(alpha = 0.2, color = 'red' ) ,
             ggplot(data = redwine, aes(volatile.acidity, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(), ncol=2)
```

Volatile acidity has quite a few outliers with high values.

```{r echo=FALSE, message=FALSE, warning=FALSE}

grid.arrange(ggplot(data = redwine, aes( x = 1, y = citric.acid ) ) + 
               geom_jitter(alpha = 0.2 ) +
               geom_boxplot(alpha = 0.2, color = 'red' ) ,
             ggplot(data = redwine, aes(citric.acid, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(), ncol=2)
```

It looks like most of the wines have 0 g/L of citric acid. There are also spikes
at 0.02, 0.2, and 0.48.

```{r echo=FALSE, message=FALSE, warning=FALSE}
grid.arrange(ggplot(data = redwine, aes( x = 1, y = density ) ) + 
               geom_jitter(alpha = 0.2 ) +
               geom_boxplot(alpha = 0.2, color = 'red' ) ,
             ggplot(data = redwine, aes(density, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(), ncol=2)
```

Density has an almost normal distribution with few outliers.

```{r echo=FALSE, message=FALSE, warning=FALSE}

grid.arrange(ggplot(data = redwine, aes( x = 1, y = residual.sugar ) ) + 
               geom_jitter(alpha = 0.2 ) +
               geom_boxplot(alpha = 0.2, color = 'red' ) ,
             ggplot(data = redwine, aes(residual.sugar, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(), ncol=2)
```

There are many outliers with high residual sugar and the distribution is skewed
right.

```{r echo=FALSE, message=FALSE, warning=FALSE}

grid.arrange(ggplot(data = redwine, aes( x = 1, y = chlorides ) ) + 
               geom_jitter(alpha = 0.2 ) +
               geom_boxplot(alpha = 0.2, color = 'red' ) ,
             ggplot(data = redwine, aes(chlorides, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(), ncol=2)
```

Chloride distribution is skewed right and with outliers. 
I will use log transformation later on.


```{r echo=FALSE, message=FALSE, warning=FALSE}

grid.arrange(ggplot(data = redwine, aes( x = 1, y = free.sulfur.dioxide ) ) + 
               geom_jitter(alpha = 0.2 ) +
               geom_boxplot(alpha = 0.2, color = 'red' ) ,
             ggplot(data = redwine, aes(free.sulfur.dioxide, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(), ncol=2)
```

```{r echo=FALSE, message=FALSE, warning=FALSE}

grid.arrange(ggplot(data = redwine, aes( x = 1, y = total.sulfur.dioxide ) ) + 
               geom_jitter(alpha = 0.2 ) +
               geom_boxplot(alpha = 0.2, color = 'red' ) ,
             ggplot(data = redwine, aes(total.sulfur.dioxide, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(), ncol=2)
```

Distribution for free.sulfur.dioxide and total.sulfur.dioxide is skewed right.
Log transformation can be applied  on free.sulfur.dioxide and 
total.sulfur.dioxide.

```{r echo=FALSE, message=FALSE, warning=FALSE}

grid.arrange(ggplot(data = redwine, aes( x = 1, y = sulphates ) ) + 
               geom_jitter(alpha = 0.2 ) +
               geom_boxplot(alpha = 0.2, color = 'red' ) ,
             ggplot(data = redwine, aes(sulphates, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(), ncol=2)

```

Distribution for sulphates is skewed right with some outliers.

```{r echo=FALSE, message=FALSE, warning=FALSE}

grid.arrange(ggplot(data = redwine, aes( x = 1, y = alcohol ) ) + 
               geom_jitter(alpha = 0.2 ) +
               geom_boxplot(alpha = 0.2, color = 'red' ) ,
             ggplot(data = redwine, aes(alcohol, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(), ncol=2)
```

The alcohol distribution is right-skewed. It will be interesting to see whether 
different quality wines have distinct alcohol content.

```{r echo=FALSE, message=FALSE, warning=FALSE}
p1 <- ggplot(data = redwine, aes(chlorides, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram() + scale_x_log10() + xlab('log10(chlorides)')

p2 <- ggplot(data = redwine, aes(free.sulfur.dioxide, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram() + scale_x_log10() + xlab('log10(free.sulfur.dioxide)')

p3 <- ggplot(data = redwine, aes(total.sulfur.dioxide, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram() + scale_x_log10() + xlab('log10(total.sulfur.dioxide)')

p4 <- ggplot(data = redwine, aes(residual.sugar, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram() + scale_x_log10() + xlab('log10(residual.sugar)')


grid.arrange(p1, p2, p3, p4, ncol = 2)
```

Taking log transformation of chorides, total.sulfur.dioxide,nresidual.sugar,
free.sulfur.dioxide makes their distribution normal.

# Univariate Analysis

### What is the structure of your dataset?

There are 1599 observation and 13 attributes in this data set. Except quality
variable which is categorical, the variables are numeric.

Other observations include:
* Most of the wine have quality 5 or 6 on the scale of 0-10
* Most of the wines have pH between 3.2 and 3.4
* Mean alcohol amount is 10.42%
* Average sugar amount is 2.54 g/dm^3 with the maximum 15.5, which means all of
the wine samples are not sweet.

### What is/are the main feature(s) of interest in your dataset?

The main feature of interest is the quality rating. I will investigate how
residual.sugar, pH, fixed.acidity, volatile.acidity correlate with the quality 
of wine.

### What other features in the dataset do you think will help support your investigation into your feature(s) of interest?
density, chlorides, alcohol


### Did you create any new variables from existing variables in the dataset?

Not yet, but I will create a new variable for ratings depending on the 
quality if wine.

### Of the features you investigated, were there any unusual distributions? \
Did you perform any operations on the data to tidy, adjust, or change the form \
of the data? If so, why did you do this?

The x column was removed as it was simply an index value of unimportance. 
The loaded data was already tidy, but Sulphates, chlorides, residual sugar and 
total/free sulphur dioxide all appeared to be long tailed and were 
log-transformed which revealed a normal distribution for each.

# Bivariate Plots Section


```{r echo=FALSE, message=FALSE, warning=FALSE}
ggcorr(redwine, nbreaks = 4, label_round = 3, palette = "RdBu", label = TRUE, label_size = 3, 
       label_color = "white", size = 3, legend.size = 10)
```

This shows the variables that correlate most highly with quality. These are:

* alcohol (positive correlation)
* sulphates (positive correlation)
* citric.acid (positive correlation)
* volatile.acidity (negative correlation)
* total.sulphur.dioxide (negative correlation)
* density (negative correlation)

I also see that alcohol which is highly correlated with quality, 
has negative correlation with density.

Let's investigate how above chemical properties affect quality of wine.

```{r echo=FALSE, message=FALSE, warning=FALSE}
q1 <- ggplot(redwine, aes(x = alcohol, y = quality)) +
  geom_jitter(alpha = 0.25) +
  geom_smooth(method = "lm")

q2 <- ggplot(redwine, aes(x = sulphates, y = quality)) +
  geom_jitter(alpha = 0.25) +
  geom_smooth(method = "lm")

q3 <- ggplot(redwine, aes(x = citric.acid, y = quality)) +
  geom_jitter(alpha = 0.25) +
  geom_smooth(method = "lm")

grid.arrange(q1, q2, q3, ncol = 1)
```

Respective correlation coefficients:

```{r echo=FALSE, message=FALSE, warning=FALSE}
cor(redwine$quality, redwine$alcohol, method = "pearson")
cor(redwine$quality, redwine$sulphates, method = "pearson")
cor(redwine$quality, redwine$citric.acid, method = "pearson")
```

We can definitely see the positive trend for alcohol % and quality of wine. 
Sulphates and citric.acid also have positive correlation with quality of wine 
but it needs further investigation.

```{r echo=FALSE, message=FALSE, warning=FALSE, fig.width=9, fig.height=6}
q1 <- ggplot(data = redwine, aes(x = quality.factor, y = alcohol)) +
  geom_boxplot(alpha = 0.3, color = 'blue') +
  geom_jitter( alpha = 0.1) +
  stat_summary(fun.y = "mean",
               geom = "point",
               color = "red",
               shape =  8,
               size = 2)

q2 <- ggplot(data = redwine, aes(x = quality.factor, y = sulphates)) +
  geom_boxplot(alpha = 0.3, color = 'blue') +
  geom_jitter( alpha = 0.1) +
  stat_summary(fun.y = "mean",
               geom = "point",
               color = "red",
               shape =  8,
               size = 2) 

q3 <- ggplot(data = redwine, aes(x = quality.factor, y = citric.acid)) +
  geom_boxplot(alpha = 0.3, color = 'blue') +
  geom_jitter( alpha = 0.1) +
  stat_summary(fun.y = "mean",
               geom = "point",
               color = "red",
               shape =  8,
               size = 2) 
grid.arrange(q1, q2, q3, nrow = 1)
```

We see that alcohol, sulphates, and citric.acid are positively correlated to 
quality of wine, but there are some outliers on the higher end of alcohol and 
sulphates for the wine of rating 5 for the quality. This says there might be 
other factors which decide the quality of the wine.


```{r echo=FALSE, message=FALSE, warning=FALSE}
q1 <- ggplot(redwine, aes(x = volatile.acidity, y = quality)) +
  geom_jitter(alpha = 0.25) +
  geom_smooth(method = "lm")

q2 <- ggplot(redwine, aes(x = total.sulfur.dioxide, y = quality)) +
  geom_jitter(alpha = 0.25) +
  geom_smooth(method = "lm")

q3 <- ggplot(redwine, aes(x = density, y = quality)) +
  geom_jitter(alpha = 0.25) +
  geom_smooth(method = "lm")

grid.arrange(q1, q2, q3, ncol = 1)
```

Respective correlation coefficients:

```{r echo=FALSE, message=FALSE, warning=FALSE}
cor(redwine$quality, redwine$volatile.acidity, method = "pearson")
cor(redwine$quality, redwine$total.sulfur.dioxide, method = "pearson")
cor(redwine$quality, redwine$density, method = "pearson")
```

```{r echo=FALSE, message=FALSE, warning=FALSE, fig.width=9, fig.height=6}
q1 <- ggplot(data = redwine, aes(x = quality.factor, y = volatile.acidity)) +
  geom_boxplot(alpha = 0.3, color = 'blue') +
  geom_jitter( alpha = 0.1) +
  stat_summary(fun.y = "mean",
               geom = "point",
               color = "red",
               shape =  8,
               size = 2)

q2 <- ggplot(data = redwine, aes(x = quality.factor, y = total.sulfur.dioxide)) +
  geom_boxplot(alpha = 0.3, color = 'blue') +
  geom_jitter( alpha = 0.1) +
  stat_summary(fun.y = "mean",
               geom = "point",
               color = "red",
               shape =  8,
               size = 2) 

q3 <- ggplot(data = redwine, aes(x = quality.factor, y = density)) +
  geom_boxplot(alpha = 0.3, color = 'blue') +
  geom_jitter( alpha = 0.1) +
  stat_summary(fun.y = "mean",
               geom = "point",
               color = "red",
               shape =  8,
               size = 2) 
grid.arrange(q1, q2, q3, nrow = 1)
```

We see that volatile.acidity, total.sulphur.dioxide and density are inversely 
correlated to quality of the wine but there are some outliers which shows that 
there are other factors which decide the quality of the wine.


```{r echo=FALSE, message=FALSE, warning=FALSE}
d1 <- ggplot(redwine, aes(x = density, y = alcohol)) +
  geom_jitter(alpha = 0.25) +
  geom_smooth(method = "lm")

d2 <- ggplot(redwine, aes(x = density, y = fixed.acidity)) +
  geom_jitter(alpha = 0.25) +
  geom_smooth(method = "lm")

grid.arrange(d1, d2, nrow = 1)
```

Alcohol and fixed.acidity are positively correlated to quality of wine and 
from the above plot I see that lower the density, higher the alcohol % and 
fixed.acidity.

```{r echo=FALSE, message=FALSE, warning=FALSE}
q1 <- ggplot(redwine, aes(x = volatile.acidity, y = pH)) +
  geom_jitter(alpha = 0.25) +
  geom_smooth(method = "lm")

q2 <- ggplot(redwine, aes(x = fixed.acidity, y = pH)) +
  geom_jitter(alpha = 0.25) +
  geom_smooth(method = "lm")

grid.arrange(q1, q2, nrow = 1)
```

From the above plots we can see that that quality of the wine and 
volatile.acidity are inversely correlated and volatile.acidity are directly 
correlated with pH, which shows that wine may be turning into vinegar and it 
can ruin the wine. On the contrary, fixed.acidity is good for the wine.
Most of the acids involved with wine are fixed acids with the notable exception
of acetic acid, mostly found in vinegar, which is volatile and can contribute 
to the wine fault also known as volatile acidity.


# Bivariate Analysis

### Talk about some of the relationships you observed in this part of the \
investigation. How did the feature(s) of interest vary with other features in \
the dataset?

I observed that alcohol, sulphates, and citric.acid are positively correlated 
to quality of wine and volatile.acidity, total.sulphur.dioxide and density are 
inversely correlated to quality of the wine. 

### Did you observe any interesting relationships between the other features \
(not the main feature(s) of interest)?

I noticed that density and alcohol had a stronger negative correlation than 
others and I saw that lower the density, higher the alcohol %.

### What was the strongest relationship you found?

The strongest relationship I found was between density and fixed acidity with 
r = 0.67. For the wine quality, alcohol level was the chemical property with 
the highest correlation with r = 0.47.


# Multivariate Plots Section

Levels of Wine quality:

```{r echo=FALSE, message=FALSE, warning=FALSE}
table(redwine$quality.factor)
```

As we can see that there are 6 levels on quality in our dataset, I will create 
an new variable called 'ratings' with levels 'good' for quality 7 and 8, 
'average' for quality of 5 and 6 and 'bad' for quality of 3 and 4.

```{r echo=FALSE, message=FALSE, warning=FALSE}
redwine$rating <- ifelse(redwine$quality <= 4, "bad", 
                              (ifelse(redwine$quality <= 6, "average", "good")))

redwine$rating <- factor(redwine$rating, levels = c("bad", "average", "good"))
table(redwine$rating)
```


```{r echo=FALSE, message=FALSE, warning=FALSE, fig.width=9, fig.height=6}
ggplot(data = redwine,
       aes(x = log10(sulphates), y = citric.acid,
           color = quality.factor)) +
  geom_point() + 
  facet_wrap(~rating) +
  geom_smooth(method = "lm") +
  scale_colour_brewer() +
  scale_fill_brewer(direction = -1) + theme_dark()
```

I see that lower quantity of citric acid results in bad quality of wine but 
there should be very fine balance which makes wine of good or average quality.

```{r echo=FALSE, message=FALSE, warning=FALSE, fig.width=9, fig.height=6}
ggplot(data = redwine,
       aes(x = alcohol, y = density,
           color = quality.factor)) +
  geom_point() + 
  facet_wrap(~rating) +
  geom_smooth(method = "lm") +
  scale_colour_brewer() +
  scale_fill_brewer(direction = -1) + theme_dark()
```

I see the similar pattern but in inverse. There should be fine balance of 
density and alcohol which makes wine good from an average quality.

```{r echo=FALSE, message=FALSE, warning=FALSE, fig.width=9, fig.height=6}
ggplot(data = redwine,
       aes(x = volatile.acidity, y = citric.acid,
           color = quality.factor)) +
  coord_cartesian(xlim = c(0, 1.1), ylim = c(0, 0.625)) +
  geom_point() +
  facet_wrap(~rating) +
  geom_smooth(method = "lm") +
  scale_colour_brewer() +
  scale_fill_brewer(direction = -1) + theme_dark()

by(redwine$volatile.acidity, redwine$rating, summary)
```

As mentioned above, the median volatile acidity was 0.68 for bad wines, 0.54 
for average wines, and 0.37 for excellent wines. For the upper right cluster 
under bad wines, we see that the higher alcoholic content of the wines cannot 
offset the high volatile acidity -- that being greater than 0.8 g / dm^3.


```{r echo=FALSE, message=FALSE, warning=FALSE, fig.width=9, fig.height=6}
ggplot(data = redwine,
       aes(x = fixed.acidity, y = pH, color = quality.factor)) +
  geom_point() +
  facet_wrap(~rating) +
  geom_smooth(method = "lm") +
  scale_colour_brewer() +
  scale_fill_brewer(direction = -1) + theme_dark()
```

And I can see that lower the pH, higher the acidity.

# Multivariate Analysis

### Talk about some of the relationships you observed in this part of the \
investigation. Were there features that strengthened each other in terms of \
looking at your feature(s) of interest?

I observed that higher quantity of sulphates and citric.acid makes a good wine
but with lower volatile acidity of course. Lower density and higher alcohol 
content makes a good wine.

### Were there any interesting or surprising interactions between features?

A well balanced combination of pH and fixed.acidity makes good wine.


------

# Final Plots and Summary

### Plot One
```{r echo=FALSE, Plot_One}
ggplot(data = redwine, aes(x = quality.factor, color = I('black'), fill = I('#E73C44'))) +
  geom_histogram(stat = "count") +
  ggtitle("Distribution of wine dataset across different levels of quality")
```

### Description One
Most of the wines in this dataset are of average quality i.e. 5 and 6.

### Plot Two
```{r echo=FALSE, Plot_Two, fig.width=9, fig.height=6}


q1 <- ggplot(data = redwine, aes(x = quality.factor, y = alcohol)) +
  geom_boxplot(alpha = 0.3, color = 'blue') +
  geom_jitter( alpha = 0.1) +
  stat_summary(fun.y = "mean",
               geom = "point",
               color = "red",
               shape =  8,
               size = 2)

q2 <- ggplot(data = redwine, aes(x = quality.factor, y = sulphates)) +
  geom_boxplot(alpha = 0.3, color = 'blue') +
  geom_jitter( alpha = 0.1) +
  stat_summary(fun.y = "mean",
               geom = "point",
               color = "red",
               shape =  8,
               size = 2) 

q3 <- ggplot(data = redwine, aes(x = quality.factor, y = citric.acid)) +
  geom_boxplot(alpha = 0.3, color = 'blue') +
  geom_jitter( alpha = 0.1) +
  stat_summary(fun.y = "mean",
               geom = "point",
               color = "red",
               shape =  8,
               size = 2) 

grid.arrange(q1, q2, q3, nrow = 1, top = "Relationship of Wine quality with alcohol %, sulphates and citric acid")

```

### Description Two
From the above plot I can confirm that Quality of the Red wine is directly
proportional to Alcohol %, sulphates and citric acid. Since most of the wines
were of average quality, we see that there are some outliers for quality of 
5 and 6, which says that there are other contributing factors which makes wine 
good from average quality.

### Plot Three
```{r echo=FALSE, Plot_Three, fig.width=7, fig.height=10}
f1 <- ggplot(data = redwine,
       aes(x = log10(sulphates), y = citric.acid,
           color = quality.factor)) +
  geom_point() + 
  facet_wrap(~rating) +
  geom_smooth(method = "lm") + 
  ylab("citric acid (g / dm^3)") +
  xlab("log10(sulphates) g / dm3") +
  scale_colour_brewer() +
  scale_fill_brewer(direction = -1) + theme_dark()

f2 <- ggplot(data = redwine,
       aes(x = fixed.acidity, y = pH, color = quality.factor)) +
  geom_point() +
  facet_wrap(~rating) +
  geom_smooth(method = "lm") + 
  ylab("pH") +
  xlab("fixed.acidity (g / dm^3)") +
  scale_colour_brewer() +
  scale_fill_brewer(direction = -1) + theme_dark()

grid.arrange(f1, f2, ncol = 1, top = "Effects of citric acid, sulphates, pH and fixed acidity on wine quality")
```

### Description Three

In the above I see that citric acid and sulphates have positive effect on the 
quality of the Red wine and it shows how the right combination of two variables 
can produce a higher quality wine.
Whereas higher pH can make wine bad, here it is important to remember that the 
lower pH values mean more acidic, although one would probably guess that the 
more fixed acidity there is, the more acidic the wine is. Acids are major wine 
constituents and contribute greatly to its taste.  In fact, acids impart the 
sourness or tartness that is a fundamental feature in wine taste

------

# Reflection

The red wine data set contains information on 1599 Portuguese Red Wines. 
To understand what chemical properites affect their quality, I started by
exploring the relationship individual variables with quality and seeing which 
ones correlated most highly with the quality rating. Based on these findings, 
I explored the data further, concentrating on the effect of alcohol, 
volatile acidity, sulphates content and citric acid. My findings showed that 
most good red wines have a high alcohol, sulphate and citric acid levels and 
low volatile acidity.

Using a combination of box and scatter plots in the bivariate analysis, it 
could be rather clear as to what amounts of each variable produces the best 
quality of wine. When correlation and summary charts are added to the analysis, 
our understanding of the data became even stronger.

Limitation of the current analysis is that the current data consists of samples 
collected from a specific portugal region. It will be intresting to obtain 
datasets across various wine making regions to eliminate any bias created by any
specific qualities of the product. There might be other factors such as 
region and climate which may also have effect on wine quality.

Further work could be carried out to build a linear model to predict the 
quality of red wine.

# References

* https://en.wikipedia.org/wiki/Wine_chemistry
* https://en.wikipedia.org/wiki/Acids_in_wine
* http://waterhouse.ucdavis.edu/whats-in-wine
