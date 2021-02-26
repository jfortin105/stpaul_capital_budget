---
title: "data_experimentation"
author: "Josh Fortin"
date: "2/26/2021"
output: 
  html_document:
    keep_md: TRUE
    toc: TRUE
    toc_float: TRUE
    df_print: paged
    code_download: true
---




```r
library(readr)
library(tidyverse)
```

```
## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --
```

```
## v ggplot2 3.3.2     v dplyr   1.0.2
## v tibble  3.0.4     v stringr 1.4.0
## v tidyr   1.1.2     v forcats 0.5.0
## v purrr   0.3.4
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
library(patchwork)
```


```r
Adopted_Capital_Improvement_Budgets <- read_csv("Adopted_Capital_Improvement_Budgets.csv")
```

```
## 
## -- Column specification --------------------------------------------------------
## cols(
##   YEAR = col_double(),
##   SERVICE = col_character(),
##   TITLE = col_character(),
##   `ID #` = col_character(),
##   DEPARTMENT = col_character(),
##   LOCATION = col_character(),
##   DISTRICT = col_character(),
##   Description = col_character(),
##   FIN_CODE = col_character(),
##   Amount = col_double(),
##   LATITUDE = col_double(),
##   LONGITUDE = col_double()
## )
```


```r
g1 <- Adopted_Capital_Improvement_Budgets %>% 
  add_count(SERVICE, name = "counts") %>% 
  mutate(s_reorder = fct_reorder(SERVICE, counts, .fun = max)) %>% 
  ggplot(aes(y = s_reorder)) +
  geom_bar() +
  labs(title = "Amount of Projects by Service Type",
       x = "",
       y = "") 

g2 <- Adopted_Capital_Improvement_Budgets %>% 
  mutate(Service_reorder = fct_reorder(SERVICE, Amount, sum)) %>% 
  ggplot(aes(x = Amount, y = Service_reorder)) +
  geom_col() +
  labs(title = "Costs of Projects by Service Type ($)",
       x = "",
       y = "") +
  scale_x_continuous(labels = scales::comma) +
  theme(axis.text.x = element_text(angle = 30))

g2 / g1
```

![](data_experimentation_files/figure-html/Projects by Service Type-1.png)<!-- -->

