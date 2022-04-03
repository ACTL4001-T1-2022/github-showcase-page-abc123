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

R was used to clean/standardise the raw datasets initially. Defending, passing, and shooting data were joined on players and year from the raw data to create a dataset with all statistics for each player as well as their salary data.
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

ld21 <- ld %>% filter(Year==2021)
lg21 <- lg %>% filter(Year==2021)
lp21 <- lp %>% filter(Year==2021)
ls21 <- ls %>% filter(Year==2021)

ld20 <- ld %>% filter(Year==2020)
lg20 <- lg %>% filter(Year==2020)
lp20 <- lp %>% filter(Year==2020)
ls20 <- ls %>% filter(Year==2020)

ldp21 <- left_join(ld21,lp21,by=c("Player","Nation","Pos","Squad","League","Year"))
ldps21 <- left_join(ldp21,ls21,by=c("Player","Nation","Pos","Squad","League","Year"))
ldpss21 <- left_join(ldps21,sal21,by=c("Player"))
lgs21 <- left_join(lg21,sal21,by=c("Player"))

ldp20 <- left_join(ld20,lp20,by=c("Player","Nation","Pos","Squad","League","Year"))
ldps20 <- left_join(ldp20,ls20,by=c("Player","Nation","Pos","Squad","League","Year"))
ldpss20 <- left_join(ldps20,sal20,by=c("Player"))
lgs20 <- left_join(lg20,sal20,by=c("Player"))

ldpss <- read.csv("ldpssmerged.csv",header=T,stringsAsFactors=T)

write.csv(ldpss20,"ldpss20.csv")
write.csv(ldpss21,"ldpss21.csv")
write.csv(lgs20,"lgs20.csv")
write.csv(lgs21,"lgs21.csv")

ldpss <- union_all(ldpss20, ldpss21)
ldpss[is.na(ldpss)] <- 0
ldpss[is.negative(ldpss)] <- 0

forwards <- ldpss %>% filter(position=="F")
defenders <- ldpss %>% filter(position=="D")
midfields <- ldpss %>% filter(position=="M")
goalkeep <- ldpss %>% filter(position=="G")

ldpss[is.na(ldpss)] <- 0
ldpss[is.negative(ldpss)] <- 0
```
---

## Assumptions


## Methodology


## Economic Impacts


## Risks and Limitations


>Now it's time to build your own website to showcase your work.  
>To create a website on GitHub Pages to showcase your work is very easy.

This is written in markdown language. 
>
* Click [4001 link](https://classroom.github.com/a/ggiq0YzO) to accept your group assignment.
* Click [5100 link](https://classroom.github.com/a/uVytCqDv) to accept your group assignment 

![](200w.gif)

#### Follow the [guide doc](Doc1.pdf) to submit your work. 
---
>Be creative! Feel free to link to embed your [data](player_data_salaries_2020.csv), [code](sample-data-clean.ipynb), [image](ACC.png) here

More information on GitHub Pages can be found [here](https://pages.github.com/)
