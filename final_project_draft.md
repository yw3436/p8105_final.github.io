Final Project Draft
================
Xiaoyu Li

``` r
library(tidyverse)
library(readxl)
library(patchwork)

knitr::opts_chunk$set(
  fig.width = 8,
  fig.asp = .6,
  fig.height = 8,
  out.width = "90%"
)

theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)

scale_colour_discrete = scale_colour_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

## Import the crime data

``` r
crime_df = 
  read_xls(
  "./data/seven-major-felony-offenses-by-precinct-2000-2019.xls",
  range = "B3:V619") %>% 
  filter(CRIME == "TOTAL SEVEN MAJOR FELONY OFFENSES")

pct_df =
  read_xls(
  "./data/seven-major-felony-offenses-by-precinct-2000-2019.xls",
  range = "A3:A619") %>% 
  drop_na(PCT)


total_crime_df =
  crime_df %>% 
  mutate(PCT = pct_df$PCT) %>% 
  select(PCT, "2013":"2019") %>% 
  pivot_longer("2013":"2019", names_to = "year", values_to = "total_crime") %>%
  mutate(borough = case_when(
    PCT <= 34 ~ "Manhattan",
    between(PCT, 40, 52) ~ "Bronx",
    between(PCT, 60, 94) ~ "Brooklyn",
    between(PCT, 100, 115) ~ "Queens",
    between(PCT, 120, 123) ~ "Staten Island"
  )) %>%
  group_by(borough, year) %>% 
  summarize(total_crime = sum(total_crime)) %>%  
  mutate(year = as.integer(year))
```

    ## `summarise()` regrouping output by 'borough' (override with `.groups` argument)

``` r
write.csv(total_crime_df, "./data/total_crime.csv")
```

*“total\_crime” is the total number of 7 major felonies*

Import census data

``` r
census_df = 
  read_csv("./data/Census_Demographics_at_the_Neighborhood_Tabulation_Area__NTA__level.csv") %>% 
  select("Geographic Area - Borough", "Total Population 2010 Number") %>% 
  rename(borough = "Geographic Area - Borough",
         population_2010 = "Total Population 2010 Number"
  ) %>% 
  group_by(borough) %>% 
  summarize(population_2010 = sum(population_2010)) %>% 
  drop_na(population_2010)
```

    ## Parsed with column specification:
    ## cols(
    ##   `Geographic Area - Borough` = col_character(),
    ##   `Geographic Area - 2010 Census FIPS County Code` = col_double(),
    ##   `Geographic Area - Neighborhood Tabulation Area (NTA)* Code` = col_character(),
    ##   `Geographic Area - Neighborhood Tabulation Area (NTA)* Name` = col_character(),
    ##   `Total Population 2000 Number` = col_double(),
    ##   `Total Population 2010 Number` = col_double(),
    ##   `Total Population Change 2000-2010 Number` = col_double(),
    ##   `Total Population Change 2000-2010 Percent` = col_double()
    ## )

    ## `summarise()` ungrouping output (override with `.groups` argument)

``` r
write.csv(census_df, "./data/census.csv")
```
