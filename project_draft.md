---
title: "project_draft"
author: "Josh Fortin"
date: "3/6/2021"
output: 
  html_document:
    theme: united
    keep_md: TRUE
    toc: TRUE
    df_print: paged
    code_download: true
    code_folding: hide
---




```
## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --
```

```
## v ggplot2 3.3.3     v dplyr   1.0.4
## v tibble  3.1.0     v stringr 1.4.0
## v tidyr   1.1.2     v forcats 0.5.0
## v purrr   0.3.4
```

```
## Warning: package 'ggplot2' was built under R version 4.0.4
```

```
## Warning: package 'dplyr' was built under R version 4.0.4
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following objects are masked from 'package:base':
## 
##     date, intersect, setdiff, union
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


# Introduction and Background

This dataset comes from the [City of Saint Paul](https://information.stpaul.gov/City-Administration/Adopted-Capital-Improvement-Budgets-Dataset/c6jd-rwmu) and contains projects that were adopted as part of the City’s capital improvement budget from 2004 - 2021. This dataset contains 1,931 rows, each representing a single project or budget item, and 12 columns which describe different aspects of these projects / budget items. To clarify, the year attributed to each project / budget item delineates the year the project / budget item was adopted into the CIB, not the date of its completion. 

The Capital Improvement Budget is the system that the City of Saint Paul uses to fund the maintenance and construction of municipal infrastructure. The CIB is created by the 18 residents of Saint Paul who make up the Capital Improvement Budget Committee. The CIB Committee creates the CIB by reviewing and recommending projects, along with funding levels, to the Mayor for eventual approval by the Mayor and City Council as part of the larger budget process. The types of projects reviewed and submitted follow the guidelines set by the City: 

> “ Projects are eligible if they finance the acquisition, betterment, physical development, redevelopment and other improvement of City-owned land and buildings, and have a useful life of at least ten years.” 

Underlying this system is a two-year cycle for proposals that was created in 2019, where both City departments and the community have separate years to submit Capital Improvement projects to the CIB committee. Additional information regarding this cycle, the CIB committee, and the placement of the CIB in the larger City of Saint Paul Budget can be found at the Capital Improvement Budget Process [website](https://www.stpaul.gov/departments/financial-services/capital-improvement-budget-process). 
As someone who is interested in municipal infrastructure and a resident of Saint Paul, I am interested in seeing where our City is investing in its capital infrastructure. To this end, I am interested in a few different questions:

> How has the CIB changed over time?

>What kinds of services are CIB projects providing? 

>How are projects distributed across City Departments?

# Capital Improvement Budget Over Time


```r
Adopted_Capital_Improvement_Budgets %>% 
  group_by(YEAR) %>% 
  summarize(year_amount = sum(Amount)) %>% 
  ggplot(aes(x = YEAR, y = year_amount)) +
  geom_col(fill = "#FDB462") +
  scale_y_continuous(labels = scales::dollar) +
  labs(title = "Yearly Adopted Capital Improvement Budget",
       subtitle = "2004 - 2021",
       y = "",
       x = "") +
  theme(plot.title.position = "plot",
        plot.background = element_rect("linen"), 
        panel.background = element_rect("linen"),
        legend.background = element_rect("linen"),
        axis.ticks.y = element_blank(),
        panel.grid.major.y = element_line("grey70"),
        panel.grid.minor.y = element_line("grey85"),
        panel.grid.major.x = element_blank(),
        panel.grid.minor.x = element_blank())
```

![](project_draft_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

We can learn a few things from this visualization. First, we can see that there's a lot of variation in the yearly Adopted Capital Improvement Budget. Across the 17 year range of data, the smallest Adopted Capital Improvement Budget was only $40,148,048 in 2012 -  while the largest occured only 2 years later in 2014 at $123,223,000. Additionally, we can see that since the adoption of the new CIB committee goals and two year budget cycle in 2019 that there has been a steady increase in the cost of the CIB. While only 3 years of data is not enough to establish the existence of a trend like this, it is something that will be interesting to keep an eye on as the years progress and the CIB Committee continues these new policies

# What Kinds of Services do Capital Improvement Projects Provide?

This data set includes a categorical variable, SERVICE, which describes the type of service that the project / budget item provides to the community, broken down into 4 categories:

> Community Facilities

> Residential and Economic Development

> Streets and Utilities

> Internal Service

In the visualizations below, I have broken down the distribution of projects by service type in two ways: by the total amount of projects in each service type, and the total cost of projects in each service type


```r
g1 <- Adopted_Capital_Improvement_Budgets %>% 
  add_count(SERVICE, name = "counts") %>% 
  mutate(s_reorder = fct_reorder(SERVICE, counts, .fun = max)) %>% 
  ggplot(aes(y = s_reorder)) +
  geom_bar(fill = "#FDB462") +
  labs(title = "Total Amount of Capital Improvement Projects by Service Type",
       x = "",
       y = "") +
  theme(plot.title.position = "plot",
        plot.background = element_rect("linen"), 
        panel.background = element_rect("linen"),
        legend.background = element_rect("linen"),
        axis.ticks.x = element_blank(),
        axis.ticks.y = element_blank(),
        panel.grid.major.x = element_line("grey70"),
        panel.grid.minor.x = element_line("grey85"),
        panel.grid.major.y = element_blank())

g2 <- Adopted_Capital_Improvement_Budgets %>% 
  mutate(Service_reorder = fct_reorder(SERVICE, Amount, sum)) %>% 
  ggplot(aes(x = Amount, y = Service_reorder)) +
  geom_col(fill = "#FDB462") +
  labs(title = "Total Costs of Capital Improvement Projects by Service Type",
       x = "",
       y = "") +
  scale_x_continuous(labels = scales::dollar) +
  theme(plot.title.position = "plot",
        plot.background = element_rect("linen"), 
        panel.background = element_rect("linen"),
        legend.background = element_rect("linen"),
        axis.ticks.x = element_blank(),
        axis.ticks.y = element_blank(),
        panel.grid.major.x = element_line("grey70"),
        panel.grid.minor.x = element_line("grey85"),
        panel.grid.major.y = element_blank())

service_g <- g1 / g2
service_g
```

![](project_draft_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

From these visualizations, we can see that Community Facilities and Streets and Utilities are by far the largest two types of services being provided by adopted Capital Improvement Budget projects. Additionally, Streets and Utilities projects are significantly more expensive than Community Facilities projects.

# How are Capital Improvement Projects Distributed Across City Departments?

Another aspect of this data worth exploring is in what City departments projects are occuring, Or, more specifically, which City departments are implementing different CIB projects. Unfortunately, the data does not provide any information about who submitted each CIB project, but looking at which departments implemented each project will give us further insight into what capital is being built in the City of Saint Paul.


```r
g3 <- Adopted_Capital_Improvement_Budgets %>% 
  add_count(DEPARTMENT, name = "counts") %>% 
  mutate(d_reorder = fct_reorder(DEPARTMENT, counts, .fun = max)) %>% 
  ggplot(aes(y = d_reorder)) +
  geom_bar(fill = "#FDB462") +
  labs(title = "Total Amount of Projects by City Department",
       x = "",
       y = "") +
  theme(plot.title.position = "plot",
        plot.background = element_rect("linen"), 
        panel.background = element_rect("linen"),
        legend.background = element_rect("linen"),
        axis.ticks.x = element_blank(),
        axis.ticks.y = element_blank(),
        panel.grid.major.x = element_line("grey70"),
        panel.grid.minor.x = element_line("grey85"),
        panel.grid.major.y = element_blank())

g4 <- Adopted_Capital_Improvement_Budgets %>% 
  mutate(department_reorder = fct_reorder(DEPARTMENT, Amount, sum)) %>% 
  ggplot(aes(x = Amount, y = department_reorder)) +
  geom_col(fill = "#FDB462") +
  labs(title = "Total Costs of Projects by City Department",
       x = "",
       y = "") +
  scale_x_continuous(labels = scales::dollar) +
  theme(plot.title.position = "plot",
        plot.background = element_rect("linen"), 
        panel.background = element_rect("linen"),
        legend.background = element_rect("linen"),
        axis.ticks.x = element_blank(),
        axis.ticks.y = element_blank(),
        panel.grid.major.x = element_line("grey70"),
        panel.grid.minor.x = element_line("grey85"),
        panel.grid.major.y = element_blank())

 department_g <- g3 / g4
 department_g
```

![](project_draft_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

An important trend to highlight is the vast amount of projects being executed by the Public Works department. Not only is the Public Works department implementing a vast number of projects, but an even wider margin of total project costs can be attributed to this department alone. Another trend to note is
the distinct groupings of departments with similar values within both the Project Costs and Amount of Projects visualizations. In the first group, we have the Public Works, Parks and Recreation, and Planning and Economic Development departments. While there are vast margins between the both the amount of projects and total costs of projects within each of these departments, all three have significantly more capital improvement projects and spend more on these projects. The second group is made up of the General Government, Library, Police, and Fire Departments. In contrast to the first group, this grouping has very similar amounts and total costs of capital improvement projects, being significantly lower than the departments in the first group, but not as low as the next group. The final group, made up of the Safety and Inspections, Financial Services, Office of Technology, Fire and Safety Services, and Public Art departments. Again, this group in general has very similar amounts and total costs of capital improvement projects. What is most interesting is how both of these grouping trends are present in both visualizations. One possible reason for this trend could be that certain projects or budget items from certain departments did not meet the temporal aspects of the capital definition set by the CIB Committee and the City of Saint Paul. In this way, these projects would be included within other budgets.

the amount and cost of capital improvement projects being implemented by the Parks and Recreation and Planning and Economic Development Departments. While these departments did not have nearly as many projects or spend as much on projects, they both did still create more and spend more on capital improvement projects than most other departments. 