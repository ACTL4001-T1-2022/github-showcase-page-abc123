# Building a Competitive National Football Team

![](soccer.gif)

## Overview

This landing page showcases the methodology behind our construction of Rarita’s national football team using statistical models and analyses the
impact of a competitive team on the country’s economy. Here, we outline several key considerations in our team selection including data cleaning, assumptions, methodology, economic impacts and risks and limitations. 

_Data cleaning and standardisation was initially conducted
to address data concerns. Several foundational assumptions were established before model fitting for team
selection and projection of key economic indices. Due to the need to monitor the effectiveness of the proposed
team in achieving the established objectives, a flexible implementation plan was established to enable Rarita to
adapt to changing circumstances and subsequently re-select the team. Finally, due to the subjective nature of the
quantitative assumptions used, a sensitivity analysis was conducted to stress test economic feasibility of the team,
before concluding with a discussion of limitations_

# Project Objectives 

The objectives of this project include: 
* blah

---

## Data Cleaning


## Assumptions


## Methodology


## Economic Impacts


## Risks and Limitations


---

>Now it's time to build your own website to showcase your work.  
>To create a website on GitHub Pages to showcase your work is very easy.

This is written in markdown language. 
>
* Click [4001 link](https://classroom.github.com/a/ggiq0YzO) to accept your group assignment.
* Click [5100 link](https://classroom.github.com/a/uVytCqDv) to accept your group assignment 

```
install.packages("tidyverse")
library(tidyverse)

ld <- read.csv("ld.csv",header=T,stringsAsFactors=T)
lg <- read.csv("lg.csv",header=T,stringsAsFactors=T)
lp <- read.csv("lp.csv",header=T,stringsAsFactors=T)
ls <- read.csv("ls.csv",header=T,stringsAsFactors=T)

td <- read.csv("td.csv",header=T,stringsAsFactors=T)
tg <- read.csv("tg.csv",header=T,stringsAsFactors=T)
tp <- read.csv("tp.csv",header=T,stringsAsFactors=T)
ts <- read.csv("ts.csv",header=T,stringsAsFactors=T)

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

ldpss <- ldpss %>% mutate(position=substr(Pos,1,1))

forwards <- ldpss %>% filter(position=="F")
defenders <- ldpss %>% filter(position=="D")
midfields <- ldpss %>% filter(position=="M")
goalkeep <- ldpss %>% filter(position=="G")

```

#### Follow the [guide doc](Doc1.pdf) to submit your work. 
---
>Be creative! Feel free to link to embed your [data](player_data_salaries_2020.csv), [code](sample-data-clean.ipynb), [image](ACC.png) here

More information on GitHub Pages can be found [here](https://pages.github.com/)
