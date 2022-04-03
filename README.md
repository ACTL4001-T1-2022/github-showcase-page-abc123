# Building a Competitive National Football Team

![](soccer.gif)

## Overview

This landing page showcases the methodology behind our construction of Rarita’s national football team using statistical models and analyses the
impact of a competitive team on the country’s economy. Here, we outline several key considerations in our team selection including data cleaning, assumptions, methodology, economic impacts and risks and limitations. 

## Project Objectives 

The objectives of this project include: 
* Ranking within Football and Sporting Association’s (FSA) top ten members within the next five years and,
* Having a high probability of winning an FSA championship within the next ten years.
By developing Rarita’s national football brand, the overarching objective was to achieve positive economic impacts
for the country over the next 10 years such as GDP growth.

## Data Cleaning

R was used to clean/standardise the raw datasets initially. 

> League defending, passing, shooting, salary, and goalkeeping statistics imported from Excel.

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
> Data separated based on year (2020 and 2021).
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
> Defending, shooting, passing, and salary data were then joined onto one dataset. 
> Goalkeeping data separated due to different stats tracked for goalkeepers.
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
> N/A and negative data were assumed to be 0, to avoid data issues in modelling steps.
```
ldpss <- union_all(ldpss20, ldpss21)
ldpss[is.na(ldpss)] <- 0
ldpss[is.negative(ldpss)] <- 0
```


## Assumptions
### Team Selection
| Factor  | Assumption |
| ------------- | ------------- |
| Salary        | Player salaries were used as a proxy for their overall ability, with better players being paid higher salaries.  |
| Salary Growth  | In line with Rarita's inflation rates. |
| Inflation Rates  | Projected using a moving average of inflation rates from the preceding 5 years.  |
| Tournament Performance  | Tournament performance is positively correlated with economic impact. |
| Loan Provisions  | The loan fee is charged recurringly on an annual basis.  |


## Methodology
### National Football Team Selected
| Player | Nation |
| --- | --- |
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

## Feature Selection

## Methodology

### Feature Selection


### Player Metric


### Comparison Against Competitors

### Economic Impacts


## Risks and Limitations

## Player Metric


## Comparison Against Competitors

## Economic Impacts


## Risks and Limitations


![](200w.gif)

