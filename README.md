# Building a Competitive National Football Team

![](soccer.gif)

## Group Members
* Lenny Han - z5258272
* Thomas Jiang - z5255865
* Zekai (Eric) Kuang - z5256982
* Celina Mak - z5255598

## Table of Contents
<!-- TABLE OF CONTENTS -->
<details open>
  <summary style = "font-size:13pt;">Table of Contents</summary>
  <ol>
    <li>
      <a href="#overview">Overview</a>
    </li>
    <li>
      <a href="#project-objectives">Project Objectives</a>
    </li>
    <li>
      <a href="#data-cleaning">Data Cleaning</a>
    </li>
    <li>
      <a href="#assumptions">Assumptions</a>
      <ol>
        <li><a href="#team-selection">Team Selection</a></li>
        <li><a href="#economic-impact">Economic Impact</a></li>
      </ol>
    </li>
    <li>
      <a href="#team-selection-methodology">Team Selection Methodology</a>
      <ol>
        <li><a href="#feature-selection">Soccer Power Index</a></li>
        <li><a href="#player-metric">Variables</a></li>
        <li><a href="#comparison-against-competitors">Comparison Against Competitors</a></li>
        <li><a href="#national-football-team-selected">National Football Team Selected</a></li>
        <li><a href="#implementation-plan">Implementation Plan</a></li>
      </ol>
     </li>
    <li>
      <a href="#economic-impacts">Economic Impacts</a>
      <ol>
        <li><a href="#gdp-projections">GDP Projections</a></li>
        <li><a href="#net-revenue-projections">Net Revenue Projections</a></li>
        <li><a href="#sensitivity-analysis">Sensitivity Analysis</a></li>
      </ol>
    </li>
    <li>
      <a href="#risks-and-mitigations">Risks and Mitigations</a>
    </li>
    <li>
  </ol>
</details>

## Overview

This landing page showcases the methodology behind the construction of Rarita’s national football team using statistical models and analyses the
impact of a competitive team on the country’s economy. Here, we outline several key considerations in our team selection including data cleaning, assumptions, methodology, economic impacts and risks and limitations. 

## Project Objectives 

The objectives of this project included: 
* Ranking within Football and Sporting Association’s (FSA) top ten members within the next five years and,
* Having a high probability of winning an FSA championship within the next ten years.

By developing Rarita’s national football brand, the overarching objective was to achieve positive economic impacts
for the country over the next 10 years such as GDP growth.

## Data Cleaning

R was used to firstly clean/standardise the raw datasets. 

> League defending, passing, shooting, salary, and goalkeeping statistics were imported from Excel.

```
install.packages("tidyverse")
library(tidyverse)

ld <- read.csv("ld.csv",header=T,stringsAsFactors=T)
lg <- read.csv("lg.csv",header=T,stringsAsFactors=T)
lp <- read.csv("lp.csv",header=T,stringsAsFactors=T)
ls <- read.csv("ls.csv",header=T,stringsAsFactors=T)

sal20 <- read.csv("sal20.csv",header=T,stringsAsFactors = T)
sal21 <- read.csv("sal21.csv",header=T,stringsAsFactors = T)

sal21 <- sal21[!duplicated(sal21),]
sal20 <- sal20[!duplicated(sal20),]
```

> The data was separated by the associated year (2020 and 2021).

```
ld21 <- ld %>% filter(Year==2021)
lg21 <- lg %>% filter(Year==2021)
lp21 <- lp %>% filter(Year==2021)
ls21 <- ls %>% filter(Year==2021)

ld20 <- ld %>% filter(Year==2020)
lg20 <- lg %>% filter(Year==2020)
lp20 <- lp %>% filter(Year==2020)
ls20 <- ls %>% filter(Year==2020)
```

> Defending, shooting, passing, and salary data were then joined onto one dataset. Goalkeeping data was separated due to the different set of measured statistics.

```
ldp21 <- left_join(ld21,lp21,by=c("Player","Nation","Pos","Squad","League","Year"))
ldps21 <- left_join(ldp21,ls21,by=c("Player","Nation","Pos","Squad","League","Year"))
ldpss21 <- left_join(ldps21,sal21,by=c("Player"))
lgs21 <- left_join(lg21,sal21,by=c("Player"))

ldp20 <- left_join(ld20,lp20,by=c("Player","Nation","Pos","Squad","League","Year"))
ldps20 <- left_join(ldp20,ls20,by=c("Player","Nation","Pos","Squad","League","Year"))
ldpss20 <- left_join(ldps20,sal20,by=c("Player"))
lgs20 <- left_join(lg20,sal20,by=c("Player"))
```

> N/A and negative data were set to equal 0 to avoid data issues in later modelling steps.

```
ldpss <- union_all(ldpss20, ldpss21)
ldpss[is.na(ldpss)] <- 0
ldpss[is.negative(ldpss)] <- 0
```

## Assumptions
### Team Selection

| Factor | Assumption |
| :---:  | :---:  |
| Salary        | Player salaries were used as a proxy for their overall ability, with more skilled players being paid higher salaries.  |
| Salary Growth  | In line with Rarita's inflation rates. |
| Inflation Rates  | Projected using a moving average of inflation rates from the preceding 5 years.  |
| Tournament Performance  | Tournament performance is positively correlated with economic impact. |
| Loan Provisions  | The loan fee is charged recurringly on an annual basis.  |

### Economic Impact

| Factor  | Assumption |
| :---:  | :---:  |
| Inflation        | Projected from 2021-31 using a 5-year moving average with projected rates of 2.5-3% over the period, which is in-line with Rarita's historical inflation trends. |
| Discount Rate | Set as 1.9%, the 10-year treasury yield as of 2021 for Rarita. |
| Commercial Revenue  | A mix of inflation and social media growth was used to project commercial revenue.|
| Broadcast Revenue  | Average annual growth over 2016-19 was used to estimate long-term growth. |
| Staff Expenses  | Estimated using existing per capital staff expenses and salary of constructed team. Projected under best-estimate of inflation for future periods.  |
| Matchday Revenue and Other Expenses | Projected using estimated inflation.  |
| Social Media Growth | Best estimate of 5% annual growth rate using baseline scenario of Rarita's tournament performance. |
| GDP Growth | Baseline economic scenario projected using linear regression model on Healthcare Spending, Savings Rate and Population Density. |

## Team Selection Methodology

### Feature Selection
A random forest model was developed to predict the salaries of players using their measured statistics to identify high performing players and select the most competitive team for Rarita.

```
library(gbm)
library(randomForest)

ball <- read.csv('ldpssmerged.csv', header = TRUE)
```

> Age and league treated as factors in model.

```
ball$Player <- NULL
ball$Age <- as.factor(ball$Age)
ball$League <- as.factor(ball$League)

ball[is.na(ball)] <- 0

ball_gk <- ball %>% filter(str_detect(ball$Pos, "GK"))
```

> Dataset portioned into training and test set with an 80:20 split.

```
set.seed(1)
training_set <- sample(length(ball$Salary), 0.8*length(ball$Salary))
train <- ball[training_set, ]
test <- ball[-training_set, ]
```

> Random Forest model fit using training and testing sets.

```
#Model fitting on training set
p <- length(ball) - 1 #Number of predictors in the data set
m <- round(sqrt(p))
rf_fit <- randomForest(as.numeric(Salary) ~ ., data = train, mtry = m, importance = TRUE)
varImpPlot(rf_fit)

#Re-run random forest model after selecting most important variables
rf_fit1 <- randomForest(as.numeric(Salary) ~ League + Pressures.Def.3rd + Pressures.Mid.3rd + Pressures.Att.3rd + Int + Clr + Long.Att + KP + Standard.Sh.90 + Standard.SoT.90 + Expected.npxG, data = train, mtry = m, importance = TRUE)
varImpPlot(rf_fit1)

#Model fitting on testing set
p <- length(train) - 1 #Number of predictors in the data set
m <- round(sqrt(p))
rf_fit_test <- randomForest(as.numeric(Salary) ~ ., data = test, mtry = m, importance = TRUE)

#Re-run random forest model after selecting most important variables
rf_fit1_test <- randomForest(as.numeric(Salary) ~ League + Pressures.Def.3rd + Pressures.Mid.3rd + Pressures.Att.3rd + Int + Clr + Long.Att + KP + Standard.Sh.90 + Standard.SoT.90 + Expected.npxG, data = test, mtry = m, importance = TRUE)
varImpPlot(rf_fit1_test)
```

> A separate model was fit for goalkeepers due to varying measured statistics.

```
p_gk <- length(ball_gk) - 1 #Number of predictors in the data set
m_gk <- round(sqrt(p_gk))
rf_fit_gk <- randomForest(as.numeric(Salary) ~ ., data = ball_gk, mtry = m_gk, importance = TRUE, na.action = na.roughfix)

varImpPlot(rf_fit_gk)

lm_fit <- lm(log(as.numeric(Salary)) ~ ., data = train)
lm_pred <- predict(lm_fit, test)
lm_comparison <- abs(lm_pred - as.numeric(test$Salary))
```

> GBM was also fit, with results compared to Random Forest.

```
b_fit <- gbm(Salary ~ ., data = train, distribution = 'gaussian', n.trees = 5000, interaction.depth = 3, shrinkage = 0.01, cv.folds = 10)
b_prob <- predict(b_fit, newdata= test, n.trees = which.min(b_fit$cv.error))
b_comparison <- abs(b_prob - as.numeric(test$Salary))
```

It was found that Random Forest performed similarly to GBM, whereby due to the potential for GBMs to overfit, the Random Forest model was chosen for feature selection in identifying top performing players. This model fitting process was repeated by excluding the least important and unstandardised variables (such as aggregated statistics rather than per 90-minute statistics) to prevent overfitting and collinearity amongst predictors.

This ultimately led us to a Random Forest model that only included the 10 most significant predictors of player salary, which were then used to build our player metric.

### Player Metric

### Comparison Against Competitors

### National Football Team Selected

| Player | Nation |
| :---:  | :---:  |
| Y. Rabinovitch | Western Niasland |
| G. Katumba | Biarizea |
| M. Nkhata | People's Land of Maneau |
| M. Rashid | Sobianitedrucy |
| C. Kakayi | Dosqaly |
| S. Rizzo | Sobianitedrucy |
| X. Takagi | Rarita |
| V. It | Mico |
| O. Nakisige | Reugha |
| A. Fekete | Byasier Pujan |
| M. Kabiru | Sobianitedrucy |
| F. Among | Biarizea |
| D. Makumbi | Rarita |
| K. Mawanda | Varijitri Isles |
| P. Anderson | Esia |
| J. Yeo | Dosqaly |
| I. Tabu | Rarita |
| K. Kazlo | Rarita |
| Z. Nyamahunge | Rarita |
| F. Ithungu | Rarita |

### Implementation Plan

## Economic Impacts 

### GDP Projections

### Net Revenue Projections

### Sensitivity Analysis 

## Risks and Limitations

Several considerations and risks were excluded from the above analyses, as they were not deemed significant at
a high-level view and would unnecessarily increase the complexity of the models. However, these factors and their
potential impacts will be briefly discussed here for completeness of our evaluation.

| Limitation | Implication |
| :---: | :---: |
| Generating revenue by loaning Raritan players not included in net revenue | Revenues are understated but best estimates show they are sufficient |
| Only salaries of new players on national teams and inflation considered in expense projections | Currently no consideration for potential fixed costs (e.g. investment in infrastructure). These expenses must be included separately in the future if required | 
| Team construction dependent on player data and metrics of competing teams, all of which only available for past periods | Opposing teams may improve significantly compared to previous years, which may our benchmarks for teambuilding irrelevant |
| Players are selected based on league statistics, so only players present in league dataset are considered in team selection | Some teams had a large number of players not present in league data and their team is unable to be evaluated by our metric. We also did not allow selection of non-league players in our team construction | 



![](200w.gif)

