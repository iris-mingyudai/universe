---
title: "GDP components evolution (WIP)"
date: 2022-09-13T17:00:20+00:00
description: analyzed changes in gdp & components across years & countries
author:
  name: Iris
  image: images/author/iris-cartoon.png
menu:
  sidebar:
    name: GDP changes
    identifier: r-stat-gdp
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






```r
UN_GDP_data  <-  read_excel(here::here("dataset", "Download-GDPconstant-USD-countries.xls"), # Excel filename
                sheet="Download-GDPconstant-USD-countr", # Sheet name
                skip=2) # Number of rows to skip
```

## Data Cleaning

-   make the dataframe into the long format

-   express all figures in billions

-   rename the indicators into something shorter


```r
head(UN_GDP_data)
```

```
## # A tibble: 6 × 51
##   CountryID Country     Indic…¹ `1970` `1971` `1972` `1973` `1974` `1975` `1976`
##       <dbl> <chr>       <chr>    <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>
## 1         4 Afghanistan Final … 5.56e9 5.33e9 5.20e9 5.75e9 6.15e9 6.32e9 6.37e9
## 2         4 Afghanistan Househ… 5.07e9 4.84e9 4.70e9 5.21e9 5.59e9 5.65e9 5.68e9
## 3         4 Afghanistan Genera… 3.72e8 3.82e8 4.02e8 4.21e8 4.31e8 5.98e8 6.27e8
## 4         4 Afghanistan Gross … 9.85e8 1.05e9 9.19e8 9.19e8 1.18e9 1.37e9 2.03e9
## 5         4 Afghanistan Gross … 9.85e8 1.05e9 9.19e8 9.19e8 1.18e9 1.37e9 2.03e9
## 6         4 Afghanistan Export… 1.12e8 1.45e8 1.73e8 2.18e8 3.00e8 3.16e8 4.17e8
## # … with 41 more variables: `1977` <dbl>, `1978` <dbl>, `1979` <dbl>,
## #   `1980` <dbl>, `1981` <dbl>, `1982` <dbl>, `1983` <dbl>, `1984` <dbl>,
## #   `1985` <dbl>, `1986` <dbl>, `1987` <dbl>, `1988` <dbl>, `1989` <dbl>,
## #   `1990` <dbl>, `1991` <dbl>, `1992` <dbl>, `1993` <dbl>, `1994` <dbl>,
## #   `1995` <dbl>, `1996` <dbl>, `1997` <dbl>, `1998` <dbl>, `1999` <dbl>,
## #   `2000` <dbl>, `2001` <dbl>, `2002` <dbl>, `2003` <dbl>, `2004` <dbl>,
## #   `2005` <dbl>, `2006` <dbl>, `2007` <dbl>, `2008` <dbl>, `2009` <dbl>, …
## # ℹ Use `colnames()` to see all variable names
```

```r
skimr::skim(UN_GDP_data)
```


Table: (\#tab:data_overview)Data summary

|                         |            |
|:------------------------|:-----------|
|Name                     |UN_GDP_data |
|Number of rows           |3685        |
|Number of columns        |51          |
|_______________________  |            |
|Column type frequency:   |            |
|character                |2           |
|numeric                  |49          |
|________________________ |            |
|Group variables          |None        |


**Variable type: character**

|skim_variable | n_missing| complete_rate| min| max| empty| n_unique| whitespace|
|:-------------|---------:|-------------:|---:|---:|-----:|--------:|----------:|
|Country       |         0|             1|   4|  34|     0|      220|          0|
|IndicatorName |         0|             1|  17|  88|     0|       17|          0|


**Variable type: numeric**

|skim_variable | n_missing| complete_rate|     mean|       sd|        p0|      p25|      p50|      p75|     p100|hist  |
|:-------------|---------:|-------------:|--------:|--------:|---------:|--------:|--------:|--------:|--------:|:-----|
|CountryID     |         0|          1.00| 4.39e+02| 2.54e+02|  4.00e+00| 2.14e+02| 4.40e+02| 6.60e+02| 8.94e+02|▇▇▇▇▆ |
|1970          |       572|          0.84| 3.28e+10| 2.03e+11| -5.68e+11| 1.47e+08| 1.03e+09| 7.60e+09| 5.51e+12|▇▁▁▁▁ |
|1971          |       573|          0.84| 3.43e+10| 2.09e+11| -3.66e+11| 1.53e+08| 1.10e+09| 8.17e+09| 5.60e+12|▇▁▁▁▁ |
|1972          |       574|          0.84| 3.63e+10| 2.20e+11| -3.88e+11| 1.57e+08| 1.13e+09| 8.68e+09| 5.87e+12|▇▁▁▁▁ |
|1973          |       573|          0.84| 3.87e+10| 2.32e+11| -4.53e+11| 1.67e+08| 1.17e+09| 9.19e+09| 6.16e+12|▇▁▁▁▁ |
|1974          |       573|          0.84| 3.96e+10| 2.33e+11| -5.66e+11| 1.79e+08| 1.32e+09| 1.00e+10| 6.17e+12|▇▁▁▁▁ |
|1975          |       574|          0.84| 4.00e+10| 2.34e+11| -2.50e+11| 1.78e+08| 1.32e+09| 1.04e+10| 6.10e+12|▇▁▁▁▁ |
|1976          |       574|          0.84| 4.21e+10| 2.45e+11| -3.08e+11| 1.90e+08| 1.38e+09| 1.09e+10| 6.36e+12|▇▁▁▁▁ |
|1977          |       574|          0.84| 4.38e+10| 2.55e+11| -3.30e+11| 1.99e+08| 1.46e+09| 1.16e+10| 6.64e+12|▇▁▁▁▁ |
|1978          |       572|          0.84| 4.56e+10| 2.67e+11| -3.26e+11| 2.08e+08| 1.50e+09| 1.19e+10| 6.96e+12|▇▁▁▁▁ |
|1979          |       573|          0.84| 4.74e+10| 2.76e+11| -3.84e+11| 2.17e+08| 1.60e+09| 1.27e+10| 7.14e+12|▇▁▁▁▁ |
|1980          |       571|          0.85| 4.83e+10| 2.78e+11| -3.39e+11| 2.29e+08| 1.61e+09| 1.29e+10| 7.15e+12|▇▁▁▁▁ |
|1981          |       568|          0.85| 4.90e+10| 2.83e+11| -3.33e+11| 2.33e+08| 1.64e+09| 1.35e+10| 7.26e+12|▇▁▁▁▁ |
|1982          |       568|          0.85| 4.93e+10| 2.85e+11| -2.79e+11| 2.32e+08| 1.63e+09| 1.36e+10| 7.26e+12|▇▁▁▁▁ |
|1983          |       568|          0.85| 5.06e+10| 2.94e+11| -4.06e+10| 2.35e+08| 1.65e+09| 1.39e+10| 7.43e+12|▇▁▁▁▁ |
|1984          |       568|          0.85| 5.28e+10| 3.09e+11| -4.38e+10| 2.47e+08| 1.72e+09| 1.45e+10| 7.91e+12|▇▁▁▁▁ |
|1985          |       567|          0.85| 5.45e+10| 3.22e+11| -8.74e+10| 2.58e+08| 1.77e+09| 1.46e+10| 8.20e+12|▇▁▁▁▁ |
|1986          |       567|          0.85| 5.64e+10| 3.33e+11| -3.50e+10| 2.74e+08| 1.80e+09| 1.50e+10| 8.47e+12|▇▁▁▁▁ |
|1987          |       566|          0.85| 5.85e+10| 3.45e+11| -2.70e+10| 2.77e+08| 1.83e+09| 1.55e+10| 8.77e+12|▇▁▁▁▁ |
|1988          |       565|          0.85| 6.12e+10| 3.60e+11| -3.60e+10| 2.86e+08| 1.92e+09| 1.56e+10| 9.19e+12|▇▁▁▁▁ |
|1989          |       547|          0.85| 6.30e+10| 3.71e+11| -2.98e+10| 3.02e+08| 1.95e+09| 1.59e+10| 9.41e+12|▇▁▁▁▁ |
|1990          |        80|          0.98| 6.01e+10| 3.58e+11| -3.53e+10| 3.63e+08| 2.27e+09| 1.57e+10| 9.57e+12|▇▁▁▁▁ |
|1991          |       161|          0.96| 5.89e+10| 3.61e+11| -3.21e+10| 3.51e+08| 2.17e+09| 1.43e+10| 9.56e+12|▇▁▁▁▁ |
|1992          |       160|          0.96| 6.00e+10| 3.70e+11| -2.99e+10| 3.48e+08| 2.14e+09| 1.47e+10| 9.76e+12|▇▁▁▁▁ |
|1993          |       160|          0.96| 6.07e+10| 3.76e+11| -4.93e+10| 3.35e+08| 2.06e+09| 1.47e+10| 9.96e+12|▇▁▁▁▁ |
|1994          |       177|          0.95| 6.29e+10| 3.89e+11| -5.84e+10| 3.55e+08| 2.11e+09| 1.52e+10| 1.03e+13|▇▁▁▁▁ |
|1995          |       170|          0.95| 6.50e+10| 3.99e+11| -1.96e+11| 3.82e+08| 2.22e+09| 1.56e+10| 1.06e+13|▇▁▁▁▁ |
|1996          |       169|          0.95| 6.72e+10| 4.12e+11| -2.55e+11| 4.00e+08| 2.26e+09| 1.63e+10| 1.10e+13|▇▁▁▁▁ |
|1997          |       170|          0.95| 6.99e+10| 4.27e+11| -2.86e+11| 4.23e+08| 2.42e+09| 1.73e+10| 1.15e+13|▇▁▁▁▁ |
|1998          |       173|          0.95| 7.17e+10| 4.42e+11| -1.91e+11| 4.51e+08| 2.64e+09| 1.74e+10| 1.19e+13|▇▁▁▁▁ |
|1999          |       173|          0.95| 7.43e+10| 4.59e+11| -2.53e+10| 4.45e+08| 2.60e+09| 1.80e+10| 1.24e+13|▇▁▁▁▁ |
|2000          |       171|          0.95| 7.77e+10| 4.77e+11| -1.26e+10| 4.73e+08| 2.75e+09| 1.90e+10| 1.29e+13|▇▁▁▁▁ |
|2001          |       171|          0.95| 7.92e+10| 4.84e+11| -4.94e+10| 4.95e+08| 2.88e+09| 1.97e+10| 1.30e+13|▇▁▁▁▁ |
|2002          |       170|          0.95| 8.09e+10| 4.93e+11| -2.82e+10| 5.30e+08| 3.04e+09| 2.01e+10| 1.32e+13|▇▁▁▁▁ |
|2003          |       172|          0.95| 8.35e+10| 5.06e+11| -2.52e+10| 5.37e+08| 3.18e+09| 2.15e+10| 1.36e+13|▇▁▁▁▁ |
|2004          |       169|          0.95| 8.75e+10| 5.24e+11| -1.89e+11| 6.03e+08| 3.40e+09| 2.28e+10| 1.40e+13|▇▁▁▁▁ |
|2005          |       133|          0.96| 9.06e+10| 5.39e+11| -1.08e+11| 6.13e+08| 3.52e+09| 2.42e+10| 1.45e+13|▇▁▁▁▁ |
|2006          |       131|          0.96| 9.49e+10| 5.56e+11| -5.09e+10| 6.53e+08| 3.77e+09| 2.59e+10| 1.49e+13|▇▁▁▁▁ |
|2007          |       131|          0.96| 9.92e+10| 5.70e+11| -2.13e+11| 7.05e+08| 4.06e+09| 2.74e+10| 1.51e+13|▇▁▁▁▁ |
|2008          |        96|          0.97| 1.00e+11| 5.71e+11| -3.26e+11| 7.44e+08| 4.29e+09| 2.88e+10| 1.51e+13|▇▁▁▁▁ |
|2009          |        96|          0.97| 9.77e+10| 5.62e+11| -1.64e+11| 6.62e+08| 4.15e+09| 2.83e+10| 1.47e+13|▇▁▁▁▁ |
|2010          |        96|          0.97| 1.03e+11| 5.80e+11| -5.11e+09| 7.39e+08| 4.40e+09| 2.96e+10| 1.50e+13|▇▁▁▁▁ |
|2011          |       114|          0.97| 1.07e+11| 5.96e+11| -9.68e+10| 7.69e+08| 4.60e+09| 3.07e+10| 1.52e+13|▇▁▁▁▁ |
|2012          |       114|          0.97| 1.09e+11| 6.10e+11| -1.19e+11| 7.56e+08| 4.76e+09| 3.16e+10| 1.56e+13|▇▁▁▁▁ |
|2013          |       131|          0.96| 1.13e+11| 6.26e+11| -1.93e+10| 7.91e+08| 5.14e+09| 3.36e+10| 1.59e+13|▇▁▁▁▁ |
|2014          |       133|          0.96| 1.16e+11| 6.45e+11| -2.45e+10| 8.16e+08| 5.34e+09| 3.38e+10| 1.62e+13|▇▁▁▁▁ |
|2015          |       134|          0.96| 1.19e+11| 6.66e+11| -1.18e+11| 8.35e+08| 5.36e+09| 3.46e+10| 1.67e+13|▇▁▁▁▁ |
|2016          |       136|          0.96| 1.22e+11| 6.84e+11| -2.95e+10| 8.56e+08| 5.49e+09| 3.50e+10| 1.70e+13|▇▁▁▁▁ |
|2017          |       143|          0.96| 1.27e+11| 7.05e+11| -2.81e+10| 8.77e+08| 5.69e+09| 3.66e+10| 1.73e+13|▇▁▁▁▁ |


```r
indicator_long <- sort(distinct(UN_GDP_data, IndicatorName)$IndicatorName)
```


```r
# create a dataframe with indicator~shorter name
indicator_match <- data.frame(IndicatorName = sort(distinct(UN_GDP_data, IndicatorName)$IndicatorName), 
                              abbrv = c(
  "category_AB", "Changes in inventories", "category_F", "Exports", 
  "Final expenditure", "Government expenditure", "Gross capital formation", "GDP", 
  "Gross fixed capital formation", "Household expenditure", "Imports", "category_D", 
  "category_CE", "category_JP", "TVA", "category_I", "category_GH")
  )
indicator_match
```

```
##                                                                               IndicatorName
## 1                                        Agriculture, hunting, forestry, fishing (ISIC A-B)
## 2                                                                    Changes in inventories
## 3                                                                     Construction (ISIC F)
## 4                                                             Exports of goods and services
## 5                                                             Final consumption expenditure
## 6                                          General government final consumption expenditure
## 7                                                                   Gross capital formation
## 8                                                              Gross Domestic Product (GDP)
## 9        Gross fixed capital formation (including Acquisitions less disposals of valuables)
## 10 Household consumption expenditure (including Non-profit institutions serving households)
## 11                                                            Imports of goods and services
## 12                                                                   Manufacturing (ISIC D)
## 13                                              Mining, Manufacturing, Utilities (ISIC C-E)
## 14                                                              Other Activities (ISIC J-P)
## 15                                                                        Total Value Added
## 16                                            Transport, storage and communication (ISIC I)
## 17                               Wholesale, retail trade, restaurants and hotels (ISIC G-H)
##                            abbrv
## 1                    category_AB
## 2         Changes in inventories
## 3                     category_F
## 4                        Exports
## 5              Final expenditure
## 6         Government expenditure
## 7        Gross capital formation
## 8                            GDP
## 9  Gross fixed capital formation
## 10         Household expenditure
## 11                       Imports
## 12                    category_D
## 13                   category_CE
## 14                   category_JP
## 15                           TVA
## 16                    category_I
## 17                   category_GH
```


```r
tidy_GDP_data <- 
  pivot_longer(data = UN_GDP_data, col = 4:51, names_to = "year", values_to = "value") %>% # 
  mutate(value = value / 1e9) %>%   # to billion
  left_join(indicator_match, by = "IndicatorName")  # get the abbreviation of indicators

glimpse(tidy_GDP_data)
```

```
## Rows: 176,880
## Columns: 6
## $ CountryID     <dbl> 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4,…
## $ Country       <chr> "Afghanistan", "Afghanistan", "Afghanistan", "Afghanista…
## $ IndicatorName <chr> "Final consumption expenditure", "Final consumption expe…
## $ year          <chr> "1970", "1971", "1972", "1973", "1974", "1975", "1976", …
## $ value         <dbl> 5.56, 5.33, 5.20, 5.75, 6.15, 6.32, 6.37, 6.90, 7.09, 6.…
## $ abbrv         <chr> "Final expenditure", "Final expenditure", "Final expendi…
```

```r
# the 3 countries for comparison
country_list <- c("United States","India", "Germany")
```

## GDP components growth overview


```r
target_ind <- c(
  "Gross fixed capital formation", 
  "Exports", "Government expenditure", 
  "Household expenditure", "Imports"
  )

tidy_GDP_data %>% 
  # filter by target countries and indicators
  filter((Country %in% country_list) & (abbrv %in% target_ind)) %>% 
  mutate(year = as.integer(year)) %>% 
  ggplot(aes(x = year, y = value, colour = abbrv, group = abbrv)) +
  geom_line() +
  scale_x_discrete(limits=c(1970, 1980, 1990, 2000, 2010)) + 
  facet_wrap(~Country) + 
  labs(title = "GDP components over time", 
       subtitle = "In constant 2010 USD", 
       y = "Billion US$", 
       x = "") + 
  scale_color_discrete(name="Components of GDP") + 
  theme_bw()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/gdp_comp_over_time-1.png" width="648" style="display: block; margin: auto;" />

## Calculated & Recorded GDP

calculate GDP using the formula below:

    GDP = Government expenditure + Gross fixed capital formation + Household expenditure + Net exports


```r
tidy_GDP_data_x <- tidy_GDP_data %>% 
  filter(abbrv %in% 
           c("Exports", "Imports", 
             "Government expenditure", 
             "Gross capital formation", 
             "Household expenditure", 
             "GDP")) %>% 
  # get GDP components, ratios / recorded_GDP, GDP_hat
  summarize(Country, year, abbrv, value) %>% 
  pivot_wider(names_from = abbrv, values_from = value) %>% 
  mutate(`Net exports` = Exports - Imports, 
         Gr = `Government expenditure` / GDP, 
         Cr = `Household expenditure` / GDP, 
         Ir = `Gross capital formation` / GDP, 
         Xr = (Exports - Imports) / GDP, 
         GDP_hat = 
           `Government expenditure` + `Household expenditure` + 
           `Gross capital formation` + (Exports - Imports)) %>% 
  summarize(Country, year, 
            `Government expenditure`, 
            `Gross capital formation`, 
            `Household expenditure`, `Net exports`, 
            Gr, Cr, Ir, Xr, 
            GDP, GDP_hat) %>% 
  # convert to longer
  pivot_longer(cols = 3:12, names_to = "components", values_to = "value")
```

> Differences between the calculated (or theoretical) GDP and the recorded one varies across countries.


```r
tidy_GDP_data_x %>% 
  pivot_wider(names_from = components, values_from = value) %>% 
  mutate(diff_perc = (GDP_hat - GDP) / GDP) %>% 
  group_by(Country) %>% 
  summarize(mean_difference = mean(diff_perc)) %>% 
  slice_max(mean_difference, n = 10)
```

```
## # A tibble: 10 × 2
##    Country                        mean_difference
##    <chr>                                    <dbl>
##  1 Libya                                   0.243 
##  2 Saint Kitts and Nevis                   0.135 
##  3 Trinidad and Tobago                     0.124 
##  4 Vanuatu                                 0.115 
##  5 St. Vincent and the Grenadines          0.106 
##  6 Palau                                   0.0994
##  7 Brunei Darussalam                       0.0986
##  8 Bhutan                                  0.0973
##  9 Bangladesh                              0.0871
## 10 Republic of Korea                       0.0780
```

```r
tidy_GDP_data_x %>% 
  pivot_wider(names_from = components, values_from = value) %>% 
  mutate(diff_perc = (GDP_hat - GDP) / GDP) %>% 
  group_by(Country) %>% 
  summarize(mean_difference = mean(diff_perc)) %>% 
  slice_min(mean_difference, n = 10)
```

```
## # A tibble: 10 × 2
##    Country                    mean_difference
##    <chr>                                <dbl>
##  1 Maldives                           -0.271 
##  2 Haiti                              -0.230 
##  3 Afghanistan                        -0.138 
##  4 Myanmar                            -0.122 
##  5 Eswatini                           -0.117 
##  6 Lao People's DR                    -0.0730
##  7 U.R. of Tanzania: Mainland         -0.0716
##  8 Oman                               -0.0653
##  9 Jordan                             -0.0640
## 10 United Arab Emirates               -0.0570
```


```r
# function for visualizing gdp difference given country list
get_gaps <- function(data,  countries) { 
  data %>% 
    filter(Country %in% countries) %>% 
    pivot_wider(names_from = components, values_from = value) %>% 
    mutate(diff_perc = (GDP_hat - GDP) / GDP) %>% 
    mutate(year = as.integer(year)) %>% 
    ggplot(aes(x = year, y = diff_perc)) +
    geom_line() +
    scale_x_discrete(limits=c(1970, 1980, 1990, 2000, 2010)) + 
    scale_y_continuous(labels = scales::percent) + 
    facet_wrap(~Country) + 
    labs(title = "% difference between calculated & recorded GDP", 
       y = "% difference", 
       x = "") + 
  theme_bw()
}
```

> Taking three countries as an example, the difference also changes over time for individual country.


```r
get_gaps(tidy_GDP_data_x, c("United States","India", "Germany"))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/gdp_gaps_eg-1.png" width="648" style="display: block; margin: auto;" />

## The GDP components trend


```r
# visualizing gdp components% changing with years, comparing countries
get_comp_plot <- function(df, countries){
  # filter by countries and indicators
  data <- df %>% 
    filter((Country %in% countries) & 
             (components %in% c("Gr", "Cr", "Ir", "Xr"))
           ) %>% 
    mutate(year = as.integer(year))
  data$components <- factor(data$components, 
                            c("Gr", "Ir", "Cr", "Xr"))
  # plotting
  ggplot(data = data, aes(x = year, y = value, colour = components, group = components)) +
    geom_line() +
    scale_x_discrete(limits=c(1970, 1980, 1990, 2000, 2010)) + 
    scale_y_continuous(labels = scales::percent) + 
    facet_wrap(~Country, nrow = 1) + 
    labs(title = "GDP and its breakdown at constant 2010 prices in US Dollar", 
         caption = "Source: United Nations, https://unstats.un.org/unsd/snaama/Downloads", 
         y = "proportion", 
         x = "") + 
    scale_color_discrete(
      labels = c("Government expenditure", "Gross capital formation", "Household expenditure", "Net exports")) +
    theme_bw() +
    theme(legend.title = element_blank())
}
```

### US, Indian, Germany


```r
get_comp_plot(tidy_GDP_data_x, c("United States","India", "Germany"))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/gdp_components_eg-1.png" width="648" style="display: block; margin: auto;" />

> For all the three, household expenditure is a primary component of GDP.

> Government expenditure in India has been far less than the other two countries. More specifically, the gross capital formation and the household expenditure exhibit opposite trends, while the government expenditure and the net exports are more stable.

> As for the net exports, Germany shows a positive net export from the 21st century, while Indian and the US shows a trade deficit. Plus, the trade deficit of the US is expanding.

### US, Indian, China


```r
get_comp_plot(tidy_GDP_data_x, c("United States","India", "China"))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/gdp_components_eg2-1.png" width="648" style="display: block; margin: auto;" />

> Since the beginning of the 21st century, the net export in China has been above zero, partly due to its cheap labour, which boosted the manufacturing industry.
