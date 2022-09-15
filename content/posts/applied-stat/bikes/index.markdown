---
title: "London Bikes (WIP)"
date: 2022-09-13T17:00:20+00:00
description: Bike
author:
  name: Iris
  image: images/author/iris-cartoon.png
menu:
  sidebar:
    name: Bikes
    identifier: r-stat-bikes
    parent: R Statistics
    weight: 10
tags: ["R", "Data Analytics"]
categories: ["R"]
math: true
output: 
  toc: true
  html_document: 
# hero: images/hero.svg
---

## The Data

``` r
#read the CSV file
bike <- read_csv(here::here("dataset", "london_bikes.csv"), 
                 show_col_types = FALSE)
```

### Cleaning & EDA

``` r
skimr::skim(bike)
```

|                                                  |      |
|:-------------------------------------------------|:-----|
| Name                                             | bike |
| Number of rows                                   | 4385 |
| Number of columns                                | 16   |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |      |
| Column type frequency:                           |      |
| character                                        | 2    |
| numeric                                          | 13   |
| POSIXct                                          | 1    |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |      |
| Group variables                                  | None |

Table 1: Data summary

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| wday          |         0 |             1 |   3 |   3 |     0 |        7 |          0 |
| month         |         0 |             1 |   3 |   3 |     0 |       12 |          0 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate |     mean |      sd |     p0 |     p25 |     p50 |     p75 |    p100 | hist  |
|:--------------|----------:|--------------:|---------:|--------:|-------:|--------:|--------:|--------:|--------:|:------|
| bikes_hired   |         0 |          1.00 | 26746.77 | 9851.14 | 2764.0 | 19598.0 | 26514.0 | 34011.0 | 73094.0 | ▃▇▅▁▁ |
| year          |         0 |          1.00 |  2016.08 |    3.49 | 2010.0 |  2013.0 |  2016.0 |  2019.0 |  2022.0 | ▆▅▇▅▇ |
| week          |         0 |          1.00 |    26.59 |   15.06 |    1.0 |    14.0 |    27.0 |    40.0 |    53.0 | ▇▇▇▇▇ |
| cloud_cover   |         2 |          1.00 |     4.89 |    2.37 |    0.0 |     3.0 |     5.0 |     7.0 |     9.0 | ▃▅▆▇▃ |
| humidity      |        21 |          1.00 |    75.48 |   11.26 |   33.0 |    67.0 |    77.0 |    84.0 |   100.0 | ▁▂▆▇▃ |
| pressure      |         0 |          1.00 | 10154.39 |  103.78 | 9731.0 | 10093.0 | 10163.0 | 10224.0 | 10477.0 | ▁▂▇▇▁ |
| radiation     |         0 |          1.00 |   120.85 |   91.12 |    8.0 |    41.0 |    97.0 |   189.0 |   402.0 | ▇▃▃▂▁ |
| precipitation |         0 |          1.00 |    16.79 |   37.34 |    0.0 |     0.0 |     0.0 |    16.0 |   516.0 | ▇▁▁▁▁ |
| snow_depth    |       271 |          0.94 |     0.02 |    0.25 |    0.0 |     0.0 |     0.0 |     0.0 |     7.0 | ▇▁▁▁▁ |
| sunshine      |         0 |          1.00 |    41.45 |   39.28 |    0.0 |     5.0 |    32.0 |    67.0 |   147.0 | ▇▃▂▂▁ |
| mean_temp     |         0 |          1.00 |    12.04 |    5.69 |   -4.1 |     7.7 |    11.9 |    16.6 |    30.9 | ▁▇▇▅▁ |
| min_temp      |         0 |          1.00 |     8.00 |    5.24 |   -9.4 |     4.1 |     8.2 |    12.2 |    22.3 | ▁▃▇▇▁ |
| max_temp      |         0 |          1.00 |    16.06 |    6.59 |   -1.2 |    11.0 |    15.8 |    21.0 |    40.2 | ▂▇▇▂▁ |

**Variable type: POSIXct**

| skim_variable | n_missing | complete_rate | min        | max        | median     | n_unique |
|:--------------|----------:|--------------:|:-----------|:-----------|:-----------|---------:|
| date          |         0 |             1 | 2010-07-30 | 2022-07-31 | 2016-07-30 |     4385 |

``` r
# fix dates using lubridate, and generate new variables for year, month, month_name, day, and day_of _week
bike <- bike %>%   
  mutate(
    year=year(date),
    month = month(date),
    month_name=month(date, label = TRUE),
    day_of_week = wday(date, label = TRUE)) %>% 
  # generate new variable season_name to turn seasons from numbers to Winter, Spring, etc
  mutate(
    season_name = case_when(
      month_name %in%  c("Dec", "Jan", "Feb")  ~ "Winter",
      month_name %in%  c("Mar", "Apr", "May")  ~ "Spring",
      month_name %in%  c("Jun", "Jul", "Aug")  ~ "Summer",
      month_name %in%  c("Sep", "Oct", "Nov")  ~ "Autumn",
    ),
    season_name = factor(season_name, 
                         levels = c("Winter", "Spring", "Summer", "Autumn"))
  )

skimr::skim(bike)
```

|                                                  |      |
|:-------------------------------------------------|:-----|
| Name                                             | bike |
| Number of rows                                   | 4385 |
| Number of columns                                | 19   |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |      |
| Column type frequency:                           |      |
| character                                        | 1    |
| factor                                           | 3    |
| numeric                                          | 14   |
| POSIXct                                          | 1    |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |      |
| Group variables                                  | None |

Table 2: Data summary

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| wday          |         0 |             1 |   3 |   3 |     0 |        7 |          0 |

**Variable type: factor**

| skim_variable | n_missing | complete_rate | ordered | n_unique | top_counts                                 |
|:--------------|----------:|--------------:|:--------|---------:|:-------------------------------------------|
| month_name    |         0 |             1 | TRUE    |       12 | Jul: 374, Jan: 372, Mar: 372, May: 372     |
| day_of_week   |         0 |             1 | TRUE    |        7 | Sun: 627, Fri: 627, Sat: 627, Mon: 626     |
| season_name   |         0 |             1 | FALSE   |        4 | Sum: 1106, Spr: 1104, Aut: 1092, Win: 1083 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate |     mean |      sd |     p0 |     p25 |     p50 |     p75 |    p100 | hist  |
|:--------------|----------:|--------------:|---------:|--------:|-------:|--------:|--------:|--------:|--------:|:------|
| bikes_hired   |         0 |          1.00 | 26746.77 | 9851.14 | 2764.0 | 19598.0 | 26514.0 | 34011.0 | 73094.0 | ▃▇▅▁▁ |
| year          |         0 |          1.00 |  2016.08 |    3.49 | 2010.0 |  2013.0 |  2016.0 |  2019.0 |  2022.0 | ▆▅▇▅▇ |
| month         |         0 |          1.00 |     6.52 |    3.45 |    1.0 |     4.0 |     7.0 |    10.0 |    12.0 | ▇▅▅▅▇ |
| week          |         0 |          1.00 |    26.59 |   15.06 |    1.0 |    14.0 |    27.0 |    40.0 |    53.0 | ▇▇▇▇▇ |
| cloud_cover   |         2 |          1.00 |     4.89 |    2.37 |    0.0 |     3.0 |     5.0 |     7.0 |     9.0 | ▃▅▆▇▃ |
| humidity      |        21 |          1.00 |    75.48 |   11.26 |   33.0 |    67.0 |    77.0 |    84.0 |   100.0 | ▁▂▆▇▃ |
| pressure      |         0 |          1.00 | 10154.39 |  103.78 | 9731.0 | 10093.0 | 10163.0 | 10224.0 | 10477.0 | ▁▂▇▇▁ |
| radiation     |         0 |          1.00 |   120.85 |   91.12 |    8.0 |    41.0 |    97.0 |   189.0 |   402.0 | ▇▃▃▂▁ |
| precipitation |         0 |          1.00 |    16.79 |   37.34 |    0.0 |     0.0 |     0.0 |    16.0 |   516.0 | ▇▁▁▁▁ |
| snow_depth    |       271 |          0.94 |     0.02 |    0.25 |    0.0 |     0.0 |     0.0 |     0.0 |     7.0 | ▇▁▁▁▁ |
| sunshine      |         0 |          1.00 |    41.45 |   39.28 |    0.0 |     5.0 |    32.0 |    67.0 |   147.0 | ▇▃▂▂▁ |
| mean_temp     |         0 |          1.00 |    12.04 |    5.69 |   -4.1 |     7.7 |    11.9 |    16.6 |    30.9 | ▁▇▇▅▁ |
| min_temp      |         0 |          1.00 |     8.00 |    5.24 |   -9.4 |     4.1 |     8.2 |    12.2 |    22.3 | ▁▃▇▇▁ |
| max_temp      |         0 |          1.00 |    16.06 |    6.59 |   -1.2 |    11.0 |    15.8 |    21.0 |    40.2 | ▂▇▇▂▁ |

**Variable type: POSIXct**

| skim_variable | n_missing | complete_rate | min        | max        | median     | n_unique |
|:--------------|----------:|--------------:|:-----------|:-----------|:-----------|---------:|
| date          |         0 |             1 | 2010-07-30 | 2022-07-31 | 2016-07-30 |     4385 |

``` r
model1 <- lm(bikes_hired~1, data = bike)
msummary(model1)
```

    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)    26747        149     180   <2e-16 ***
    ## 
    ## Residual standard error: 9850 on 4384 degrees of freedom

``` r
bike %>% 
  select(cloud_cover, humidity, pressure, radiation, precipitation, sunshine, mean_temp, min_temp) %>%
  ggpairs()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/pairing-1.png" width="648" style="display: block; margin: auto;" />

``` r
model2 <- lm(bikes_hired ~ mean_temp + humidity + pressure + radiation  + precipitation + sunshine, data= bike)
msummary(model2)
```

    ##                Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   -81710.10   11037.35   -7.40  1.6e-13 ***
    ## mean_temp        818.84      24.41   33.55  < 2e-16 ***
    ## humidity         -54.42      15.90   -3.42  0.00062 ***
    ## pressure           9.88       1.07    9.26  < 2e-16 ***
    ## radiation         12.34       2.68    4.60  4.3e-06 ***
    ## precipitation    -33.91       3.00  -11.29  < 2e-16 ***
    ## sunshine          35.74       5.01    7.13  1.2e-12 ***
    ## 
    ## Residual standard error: 6720 on 4357 degrees of freedom
    ##   (21 observations deleted due to missingness)
    ## Multiple R-squared:  0.536,  Adjusted R-squared:  0.535 
    ## F-statistic:  839 on 6 and 4357 DF,  p-value: <2e-16

``` r
model3 <- lm(bikes_hired ~ mean_temp + min_temp+ humidity + pressure + radiation  + precipitation + sunshine, data= bike)
msummary(model3)
```

    ##                Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   -69222.23   11033.93   -6.27  3.9e-10 ***
    ## mean_temp       1549.39      86.58   17.90  < 2e-16 ***
    ## min_temp        -741.86      84.42   -8.79  < 2e-16 ***
    ## humidity         -59.51      15.77   -3.77  0.00016 ***
    ## pressure           8.51       1.07    7.96  2.1e-15 ***
    ## radiation          7.97       2.70    2.95  0.00320 ** 
    ## precipitation    -33.82       2.98  -11.36  < 2e-16 ***
    ## sunshine          21.22       5.24    4.05  5.2e-05 ***
    ## 
    ## Residual standard error: 6670 on 4356 degrees of freedom
    ##   (21 observations deleted due to missingness)
    ## Multiple R-squared:  0.544,  Adjusted R-squared:  0.543 
    ## F-statistic:  743 on 7 and 4356 DF,  p-value: <2e-16

``` r
vif(model3)
```

    ##     mean_temp      min_temp      humidity      pressure     radiation 
    ##         23.74         19.16          3.10          1.21          5.96 
    ## precipitation      sunshine 
    ##          1.22          4.16

``` r
check_model(model3)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/model_colinearity-1.png" width="648" style="display: block; margin: auto;" />

``` r
model4 <- lm(bikes_hired ~ mean_temp + humidity + pressure + radiation  + precipitation + sunshine + 
               season_name+ factor(year), data= bike)
msummary(model4)
```

    ##                    Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)       -7.15e+04   8.99e+03   -7.95  2.3e-15 ***
    ## mean_temp          6.23e+02   2.57e+01   24.20  < 2e-16 ***
    ## humidity          -8.04e+01   1.31e+01   -6.12  1.0e-09 ***
    ## pressure           7.99e+00   8.69e-01    9.19  < 2e-16 ***
    ## radiation          1.58e+01   2.90e+00    5.45  5.4e-08 ***
    ## precipitation     -3.53e+01   2.43e+00  -14.54  < 2e-16 ***
    ## sunshine           2.94e+01   4.80e+00    6.13  9.8e-10 ***
    ## season_nameSpring  3.02e+02   3.21e+02    0.94     0.35    
    ## season_nameSummer  2.30e+03   4.20e+02    5.48  4.4e-08 ***
    ## season_nameAutumn  4.40e+03   2.79e+02   15.78  < 2e-16 ***
    ## factor(year)2011   3.86e+03   5.28e+02    7.31  3.1e-13 ***
    ## factor(year)2012   1.15e+04   5.26e+02   21.82  < 2e-16 ***
    ## factor(year)2013   7.32e+03   5.25e+02   13.93  < 2e-16 ***
    ## factor(year)2014   1.20e+04   5.29e+02   22.64  < 2e-16 ***
    ## factor(year)2015   1.13e+04   5.29e+02   21.43  < 2e-16 ***
    ## factor(year)2016   1.32e+04   5.28e+02   24.95  < 2e-16 ***
    ## factor(year)2017   1.33e+04   5.28e+02   25.19  < 2e-16 ***
    ## factor(year)2018   1.29e+04   5.30e+02   24.42  < 2e-16 ***
    ## factor(year)2019   1.33e+04   5.28e+02   25.14  < 2e-16 ***
    ## factor(year)2020   1.22e+04   5.34e+02   22.88  < 2e-16 ***
    ## factor(year)2021   1.50e+04   5.29e+02   28.41  < 2e-16 ***
    ## factor(year)2022   1.85e+04   5.92e+02   31.28  < 2e-16 ***
    ## 
    ## Residual standard error: 5420 on 4342 degrees of freedom
    ##   (21 observations deleted due to missingness)
    ## Multiple R-squared:   0.7,   Adjusted R-squared:  0.698 
    ## F-statistic:  482 on 21 and 4342 DF,  p-value: <2e-16

``` r
vif(model4)
```

    ##                GVIF Df GVIF^(1/(2*Df))
    ## mean_temp      3.17  1            1.78
    ## humidity       3.25  1            1.80
    ## pressure       1.21  1            1.10
    ## radiation     10.41  1            3.23
    ## precipitation  1.23  1            1.11
    ## sunshine       5.30  1            2.30
    ## season_name    5.59  3            1.33
    ## factor(year)   1.27 12            1.01

``` r
model5 <- lm(bikes_hired ~ mean_temp + humidity + pressure + radiation  + precipitation + sunshine + season_name+ factor(year) + day_of_week, data= bike)
msummary(model5)
```

    ##                    Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)       -6.88e+04   8.32e+03   -8.27  < 2e-16 ***
    ## mean_temp          6.12e+02   2.38e+01   25.69  < 2e-16 ***
    ## humidity          -8.47e+01   1.22e+01   -6.97  3.7e-12 ***
    ## pressure           7.76e+00   8.04e-01    9.65  < 2e-16 ***
    ## radiation          1.56e+01   2.69e+00    5.79  7.4e-09 ***
    ## precipitation     -3.48e+01   2.25e+00  -15.49  < 2e-16 ***
    ## sunshine           3.00e+01   4.45e+00    6.75  1.7e-11 ***
    ## season_nameSpring  3.09e+02   2.97e+02    1.04   0.2992    
    ## season_nameSummer  2.39e+03   3.88e+02    6.15  8.7e-10 ***
    ## season_nameAutumn  4.46e+03   2.58e+02   17.26  < 2e-16 ***
    ## factor(year)2011   3.91e+03   4.89e+02    7.99  1.8e-15 ***
    ## factor(year)2012   1.15e+04   4.87e+02   23.65  < 2e-16 ***
    ## factor(year)2013   7.33e+03   4.86e+02   15.07  < 2e-16 ***
    ## factor(year)2014   1.20e+04   4.90e+02   24.50  < 2e-16 ***
    ## factor(year)2015   1.14e+04   4.90e+02   23.22  < 2e-16 ***
    ## factor(year)2016   1.32e+04   4.88e+02   27.04  < 2e-16 ***
    ## factor(year)2017   1.33e+04   4.89e+02   27.32  < 2e-16 ***
    ## factor(year)2018   1.30e+04   4.90e+02   26.48  < 2e-16 ***
    ## factor(year)2019   1.33e+04   4.88e+02   27.22  < 2e-16 ***
    ## factor(year)2020   1.22e+04   4.94e+02   24.80  < 2e-16 ***
    ## factor(year)2021   1.51e+04   4.90e+02   30.76  < 2e-16 ***
    ## factor(year)2022   1.86e+04   5.48e+02   33.91  < 2e-16 ***
    ## day_of_week.L      1.56e+03   2.01e+02    7.77  9.5e-15 ***
    ## day_of_week.Q     -5.17e+03   2.01e+02  -25.75  < 2e-16 ***
    ## day_of_week.C      3.50e+02   2.01e+02    1.74   0.0812 .  
    ## day_of_week^4     -5.29e+02   2.01e+02   -2.63   0.0086 ** 
    ## day_of_week^5     -2.63e+02   2.01e+02   -1.31   0.1914    
    ## day_of_week^6      1.60e+02   2.01e+02    0.79   0.4275    
    ## 
    ## Residual standard error: 5010 on 4336 degrees of freedom
    ##   (21 observations deleted due to missingness)
    ## Multiple R-squared:  0.743,  Adjusted R-squared:  0.742 
    ## F-statistic:  465 on 27 and 4336 DF,  p-value: <2e-16

``` r
vif(model5)
```

    ##                GVIF Df GVIF^(1/(2*Df))
    ## mean_temp      3.18  1            1.78
    ## humidity       3.25  1            1.80
    ## pressure       1.21  1            1.10
    ## radiation     10.43  1            3.23
    ## precipitation  1.23  1            1.11
    ## sunshine       5.30  1            2.30
    ## season_name    5.60  3            1.33
    ## factor(year)   1.27 12            1.01
    ## day_of_week    1.01  6            1.00

``` r
check_model(model5)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/add_season_year-1.png" width="648" style="display: block; margin: auto;" />

``` r
# produce summary table comparing models using huxtable::huxreg()
huxreg(model1, model2, model3, model4, model5,
       statistics = c('#observations' = 'nobs', 
                      'R squared' = 'r.squared', 
                      'Adj. R Squared' = 'adj.r.squared', 
                      'Residual SE' = 'sigma'), 
       bold_signif = 0.05, 
       stars = NULL
) %>% 
  set_caption('Comparison of models')
```

<table class="huxtable" style="border-collapse: collapse; border: 0px; margin-bottom: 2em; margin-top: 2em; ; margin-left: auto; margin-right: auto;  " id="tab:compare">
<caption style="caption-side: top; text-align: center;">Comparison of models</caption><col><col><col><col><col><col><tr>
<th style="vertical-align: top; text-align: center; white-space: normal; border-style: solid solid solid solid; border-width: 0.8pt 0pt 0pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><th style="vertical-align: top; text-align: center; white-space: normal; border-style: solid solid solid solid; border-width: 0.8pt 0pt 0.4pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">(1)</th><th style="vertical-align: top; text-align: center; white-space: normal; border-style: solid solid solid solid; border-width: 0.8pt 0pt 0.4pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">(2)</th><th style="vertical-align: top; text-align: center; white-space: normal; border-style: solid solid solid solid; border-width: 0.8pt 0pt 0.4pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">(3)</th><th style="vertical-align: top; text-align: center; white-space: normal; border-style: solid solid solid solid; border-width: 0.8pt 0pt 0.4pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">(4)</th><th style="vertical-align: top; text-align: center; white-space: normal; border-style: solid solid solid solid; border-width: 0.8pt 0pt 0.4pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">(5)</th></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">(Intercept)</th><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0.4pt 0pt 0pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: bold;">26746.768&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0.4pt 0pt 0pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-81710.098&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0.4pt 0pt 0pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-69222.231&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0.4pt 0pt 0pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-71505.697&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0.4pt 0pt 0pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-68818.971&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(148.765)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(11037.351)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(11033.926)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(8990.221)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(8320.573)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">mean_temp</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">818.842&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">1549.392&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">622.632&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">611.748&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(24.405)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(86.578)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(25.731)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(23.813)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">humidity</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-54.425&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-59.513&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-80.382&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-84.712&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(15.899)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(15.773)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(13.127)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(12.154)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">pressure</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">9.876&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">8.514&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">7.990&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">7.764&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(1.067)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(1.069)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(0.869)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(0.804)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">radiation</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">12.336&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">7.972&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">15.815&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">15.580&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(2.680)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(2.703)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(2.904)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(2.689)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">precipitation</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-33.906&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-33.824&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-35.318&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-34.817&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(3.004)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(2.979)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(2.428)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(2.247)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">sunshine</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">35.740&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">21.217&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">29.439&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">30.018&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(5.014)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(5.238)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(4.805)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(4.449)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">min_temp</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-741.865&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(84.416)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">season_nameSpring</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">301.588&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">308.582&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">(321.169)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">(297.223)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">season_nameSummer</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">2301.106&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">2386.338&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(419.550)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(388.285)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">season_nameAutumn</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">4404.039&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">4457.221&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(279.095)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(258.249)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">factor(year)2011</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">3863.898&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">3905.515&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(528.485)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(488.985)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">factor(year)2012</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">11481.449&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">11512.140&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(526.139)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(486.812)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">factor(year)2013</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">7319.200&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">7326.365&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(525.496)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(486.216)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">factor(year)2014</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">11980.518&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">11995.788&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(529.222)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(489.665)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">factor(year)2015</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">11343.682&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">11372.777&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(529.439)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(489.867)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">factor(year)2016</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">13167.306&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">13203.897&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(527.797)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(488.346)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">factor(year)2017</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">13301.418&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">13345.630&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(528.009)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(488.542)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">factor(year)2018</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">12931.663&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">12975.158&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(529.651)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(490.066)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">factor(year)2019</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">13263.998&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">13289.574&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(527.580)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(488.145)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">factor(year)2020</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">12209.965&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">12244.452&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(533.693)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(493.803)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">factor(year)2021</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">15035.114&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">15063.643&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(529.250)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(489.692)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">factor(year)2022</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">18514.415&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">18569.929&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(591.802)</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(547.570)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">day_of_week.L</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">1559.013&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(200.566)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">day_of_week.Q</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-5166.425&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(200.667)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">day_of_week.C</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">350.260&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">(200.849)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">day_of_week^4</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">-528.594&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: bold;">(201.012)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">day_of_week^5</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">-262.751&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">(201.088)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">day_of_week^6</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">159.690&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;"></th><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0.4pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0.4pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0.4pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0.4pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0.4pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">(201.220)</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">#observations</th><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0.4pt 0pt 0pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">4385&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0.4pt 0pt 0pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">4364&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0.4pt 0pt 0pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">4364&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0.4pt 0pt 0pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">4364&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0.4pt 0pt 0pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">4364&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">R squared</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">0.000&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">0.536&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">0.544&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">0.700&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">0.743&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">Adj. R Squared</th><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">0.000&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">0.535&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">0.543&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">0.698&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; padding: 6pt 6pt 6pt 6pt; font-weight: normal;">0.742&nbsp;</td></tr>
<tr>
<th style="vertical-align: top; text-align: left; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0.8pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">Residual SE</th><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0.8pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">9851.137&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0.8pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">6724.428&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0.8pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">6666.362&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0.8pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">5419.035&nbsp;</td><td style="vertical-align: top; text-align: right; white-space: normal; border-style: solid solid solid solid; border-width: 0pt 0pt 0.8pt 0pt;    padding: 6pt 6pt 6pt 6pt; font-weight: normal;">5013.938&nbsp;</td></tr>
</table>

#### Challenge 1: Excess rentals in TfL bike sharing

Data: TfL data on how many bikes were hired every single day, from *London Data Store*

``` r
url <- "https://data.london.gov.uk/download/number-bicycle-hires/ac29363e-e0cb-47cc-a97a-e216d900a6b0/tfl-daily-cycle-hires.xlsx"

# Download TFL data to temporary file
httr::GET(url, write_disk(bike.temp <- tempfile(fileext = ".xlsx")))
```

    ## Response [https://airdrive-secure.s3-eu-west-1.amazonaws.com/london/dataset/number-bicycle-hires/2022-09-06T12%3A41%3A48/tfl-daily-cycle-hires.xlsx?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAJJDIMAIVZJDICKHA%2F20220913%2Feu-west-1%2Fs3%2Faws4_request&X-Amz-Date=20220913T184524Z&X-Amz-Expires=300&X-Amz-Signature=4c727237da25ed38a6594d4284f4384cea649f00c90f873663d5ca6e3f46a974&X-Amz-SignedHeaders=host]
    ##   Date: 2022-09-13 18:45
    ##   Status: 200
    ##   Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
    ##   Size: 180 kB
    ## <ON DISK>  /var/folders/5j/t_7j4bts6w57kqmrq8g35sx80000gn/T//RtmpznBFD5/filebc8446abe4eb.xlsx

``` r
# Use read_excel to read it as dataframe
bike0 <- read_excel(bike.temp,
                   sheet = "Data",
                   range = cell_cols("A:B"))

# change dates to get year, month, and week
bike <- bike0 %>% 
  clean_names() %>% 
  rename (bikes_hired = number_of_bicycle_hires) %>% 
  mutate (year = year(day),
          month = lubridate::month(day, label = TRUE),
          week = isoweek(day))
```

``` r
glimpse(bike)
```

    ## Rows: 4,416
    ## Columns: 5
    ## $ day         <dttm> 2010-07-30, 2010-07-31, 2010-08-01, 2010-08-02, 2010-08-0…
    ## $ bikes_hired <dbl> 6897, 5564, 4303, 6642, 7966, 7893, 8724, 9797, 6631, 7864…
    ## $ year        <dbl> 2010, 2010, 2010, 2010, 2010, 2010, 2010, 2010, 2010, 2010…
    ## $ month       <ord> Jul, Jul, Aug, Aug, Aug, Aug, Aug, Aug, Aug, Aug, Aug, Aug…
    ## $ week        <dbl> 30, 30, 30, 31, 31, 31, 31, 31, 31, 31, 32, 32, 32, 32, 32…

``` r
skimr::skim(bike)
```

|                                                  |      |
|:-------------------------------------------------|:-----|
| Name                                             | bike |
| Number of rows                                   | 4416 |
| Number of columns                                | 5    |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |      |
| Column type frequency:                           |      |
| factor                                           | 1    |
| numeric                                          | 3    |
| POSIXct                                          | 1    |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |      |
| Group variables                                  | None |

(#tab:browse_data)Data summary

**Variable type: factor**

| skim_variable | n_missing | complete_rate | ordered | n_unique | top_counts                             |
|:--------------|----------:|--------------:|:--------|---------:|:---------------------------------------|
| month         |         0 |             1 | TRUE    |       12 | Aug: 403, Jul: 374, Jan: 372, Mar: 372 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate |    mean |      sd |   p0 |   p25 |   p50 |   p75 |  p100 | hist  |
|:--------------|----------:|--------------:|--------:|--------:|-----:|------:|------:|------:|------:|:------|
| bikes_hired   |         0 |             1 | 26844.3 | 9899.73 | 2764 | 19698 | 26607 | 34206 | 73094 | ▃▇▅▁▁ |
| year          |         0 |             1 |  2016.1 |    3.51 | 2010 |  2013 |  2016 |  2019 |  2022 | ▆▅▇▅▇ |
| week          |         0 |             1 |    26.6 |   15.01 |    1 |    14 |    27 |    40 |    53 | ▇▇▇▇▇ |

**Variable type: POSIXct**

| skim_variable | n_missing | complete_rate | min        | max        | median              | n_unique |
|:--------------|----------:|--------------:|:-----------|:-----------|:--------------------|---------:|
| day           |         0 |             1 | 2010-07-30 | 2022-08-31 | 2016-08-14 12:00:00 |     4416 |

#### Bikes hired by month and year since 2015

``` r
bike %>% 
  filter(year >= 2015) %>% 
  ggplot(aes(x = bikes_hired)) + 
  geom_density() + 
  facet_grid(vars(year), vars(month)) + 
  theme(axis.text.y=element_blank(),
        axis.ticks.y=element_blank(), 
        axis.text.x = element_text(angle = 90, vjust = 1, hjust=1), 
        axis.text = element_text(size = 8)) + 
  scale_x_continuous(labels = scales::label_number(suffix = "K", scale = 0.001)) + 
  labs(x = "Bikes Hired", y = "", title = "Distribution of bikes hired per month")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/tfl_month_year_grid-1.png" width="648" style="display: block; margin: auto;" />

#### Monthly & Weekly Deviation from Expected Level

The code below creates a function to summary data with either `mean` or `median`, and then replicates graphs.

``` r
get_values <- function(df, method, base_yr, target_yr) {
  # method = mean, or method = median
  # base_yr, e.g., c(2016, 2019)
  
  # summarize data by year & week
  by_week <- df %>% 
    filter(year >= target_yr[1] & year <= target_yr[2]) %>% 
    group_by(year, week) %>% 
    summarize(real = method(bikes_hired))
  
  # summarize data by year & month
  by_month <- df %>% 
  filter(year >= target_yr[1] & year <= target_yr[2]) %>% 
  group_by(year, month) %>% 
  summarize(real = method(bikes_hired))
  
  # weekly mean/median rentals across 2016~2019
  by_week_base <- df %>% 
    filter(year >= base_yr[1] & year <= base_yr[2]) %>% 
    group_by(week) %>% 
    summarize(expected = method(bikes_hired))
  
  # monthly mean/median rentals across 2016~2019
  by_month_base <- df %>% 
    filter(year >= base_yr[1] & year <= base_yr[2]) %>% 
    group_by(month) %>% 
    summarize(expected = method(bikes_hired))
  
  # joining real value and expected value, calculating excess amount and percentage changes
  gen_relative <- function(df1, df2, by_col){
    joined <- left_join(df1, df2, by = by_col) %>% 
      mutate(excess = real - expected, 
             perc = (real - expected) / expected)
    return(joined)
  }
  by_week <- gen_relative(by_week, by_week_base, "week") %>% 
    filter(!(year == 2022 & week == 52))
  by_month <- gen_relative(by_month, by_month_base, "month")

  # graph title - median or average
  if(method(c(1, 2, 3, 4, 5, 7)) == 3.5){  # if method() returns the median
    kwd <- "median"
  } else {kwd <- "average"}
  
  # graph1
  p1 <- by_month %>% 
  mutate(
    ymax = ifelse(real >= expected, real, expected), 
    ymin = ifelse(expected > real, real, expected)
  ) %>% 
  ggplot(aes(x = month)) +
    geom_line(aes(y = expected), group = 1, color = "blue", size = 1) + 
    geom_line(aes(y = real), group = 1, color = "black", size = 0.5) + 
    geom_ribbon(aes(ymin = expected, ymax = ymax, group = 1, fill="red"), 
                alpha = 0.5) + 
    geom_ribbon(aes(ymin = ymin, ymax = expected, group = 1, fill="green"), 
                alpha = 0.5) + 
    facet_wrap(~year) + 
    labs(
      title = "Monthly changes in TfL bike rentals", 
      subtitle = paste("Change from monthly ", kwd, " shown in blue and calculated between 2016-2019"), 
      caption = "Source: TfL, London Data Store", 
      y = paste("Bike Rentals (", kwd, ")"), 
      x = ""
    ) + 
    scale_y_continuous(labels = scales::label_number(suffix = "K", scale = 0.001)) + 
    theme(aspect.ratio = 0.6, 
          legend.position="none", 
          panel.background = element_rect(fill = "white"), 
          panel.grid = element_line(colour = "light grey"),
          strip.background = element_blank(), 
          axis.text.x = element_text(angle = 90, vjust = 1, hjust=1), 
          axis.text = element_text(size = 8), 
          axis.title = element_text(size = 8), 
          plot.title = element_text(size = 10)
          )
  
  # graph2
  yaxes_min <- min(by_week$perc)
  p2 <- by_week %>% 
    mutate(
      ymax = ifelse(perc >= 0, perc, 0), 
      ymin = ifelse(0 >= perc, perc, 0), 
      colors = ifelse(perc >= 0, "red", "green")
    ) %>% 
    ggplot() + 
    geom_line(aes(x = week, y = perc), group = 1, color = "black", size = 0.5) + 
    geom_ribbon(aes(x = week, ymin = ymin, ymax = 0, group = 1, fill="green"), 
                alpha = 0.5) + 
    geom_ribbon(aes(x = week, ymin = 0, ymax = ymax, group = 1, fill="red"), 
                alpha = 0.5) + 
    facet_wrap(~year) + 
    labs(
      title = "Weekly changes in TfL bike rentals", 
      subtitle = paste("% change from weekly", kwd, "calculated between 2016-2019"), 
      caption = "Source: TfL, London Data Store", 
      y = "", 
      x = "week"
    ) + 
    scale_y_continuous(labels = scales::percent, limits = c(yaxes_min, 1.1)) + 
    scale_x_discrete(limits=c(13, 26, 39, 53)) + 
    annotate(geom = "rect", xmin = 14, xmax = 26, ymin = -Inf, ymax = Inf, fill = "grey", alpha = 0.3) + 
    annotate(geom = "rect", xmin = 40, xmax = 53, ymin = -Inf, ymax = Inf, fill = "grey", alpha = 0.3) + 
    geom_rug(mapping = aes(x = week, color = factor(colors), alpha = 0.5), sides = "b") + 
    theme(aspect.ratio = 0.6, 
          legend.position="none", 
          panel.background = element_rect(fill = "white"), 
          panel.grid = element_line(colour = "light grey"), 
          strip.background = element_blank(), 
          axis.text = element_text(size = 8), 
          axis.title = element_text(size = 8), 
          plot.title = element_text(size = 10)
          )
  p1 / p2
}
```

Clarification

-   For the **“Monthly change from monthly average”** graph,

    -   The expected average or median monthly rentals are calculated using each month’s records, regardless of year

-   For the **“Weekly change from weekly average”** graph,

    -   The average or median weekly rentals are calculated using each week’s records, regardless of year

    -   The two grey shaded rectangles correspond to Q2 (weeks 14-26) and Q4 (weeks 40-52).

``` r
get_values(bike, mean, c(2016, 2019), c(2017, 2022))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/result_bike_graph-1.png" width="100%" style="display: block; margin: auto;" />

``` r
get_values(bike, median, c(2016, 2019), c(2017, 2022))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/result_bike_graph-2.png" width="100%" style="display: block; margin: auto;" />

Although results using `mean` and `median` are similar, the average method is theoretically more suitable for evaluating expected rentals and %changes. Significant %changes are largely due to rentals surge on a few special days. The average value captures extremes while the median usually cannot.

> Reference

-   <https://ggplot2.tidyverse.org/reference/geom_ribbon.html>
-   <https://ggplot2.tidyverse.org/reference/geom_tile.html>
-   <https://ggplot2.tidyverse.org/reference/geom_rug.html>
