Unemployment
================
RongQing Jia

``` r
library(tidyverse)
library(readxl)

knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = 0.6,
  out.width = "90%"
)

theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continuous.color = "viridis",
  ggplot2.continuous.fill = "viridis"
)

sclae_color_discrete = scale_color_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

# data import and cleaning

``` r
unemp_df1 =
    read_xlsx(
    "./data/CO_LMA_MSA.xlsx",
    sheet = "County"
  ) %>% 
  janitor::clean_names() %>% 
  rename(employed = emp, unemployed = unemp, unemployment_rate = unemprate) %>% 
  mutate(
    area = as.factor(area), 
    month = as.factor(month), 
    month = recode(month, "0" = "avg")
  ) %>% 
  drop_na() 

# export tidy data frame
write.csv(unemp_df1,'./data/unemployment_rate.csv')
```
