Homework3
================

Manipulation and exploration of the *Gapminder* dataset with the `dplyr` package, complemented by visualizations made with `ggplot2`. Each task will be tackled with a table and a figure. Within `tidyverse`, the `dplyr` function works as "pliers" for data frames, allowing the modification of data without leaving the R working space. *Gapminder* is the dataset itself, while `tidyverse` is the meta-package, a collection with tools like `ggplot2` for graphics, `tbl_df` for tibble data.frames, and `dplyr` for manipulating data. Before starting the packages must be loaded. The

``` r
library(gapminder)
library(tidyverse)
```

    ## ── Attaching packages ──────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.0.0     ✔ purrr   0.2.5
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.6
    ## ✔ tidyr   0.8.1     ✔ stringr 1.3.1
    ## ✔ readr   1.1.1     ✔ forcats 0.3.0

    ## ── Conflicts ─────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

### 1. First task: Get the maximum and minimum of GDP per capita for all continents.

The piping operator `%>%` is part of the `dplyr` package, it works like nesting functions within functions, but without the use of parenthesis operators. It can be easily read from left to right. Here the *Gapminder* dataset is piped into a grouping by continent, which is in turn piped into a function that returns only the maxmimum (`maxGDP`) and minimum (`minGDP`) values per continent. The `knitr::kable()` function renders a text-ready table.

``` r
gapminder %>% group_by(continent) %>% summarize(maxGDP = max(gdpPercap), minGDP = min(gdpPercap), Mean = mean(gdpPercap), Median = median(gdpPercap)) %>%
 knitr::kable()
```

| continent |     maxGDP|      minGDP|       Mean|     Median|
|:----------|----------:|-----------:|----------:|----------:|
| Africa    |   21951.21|    241.1659|   2193.755|   1192.138|
| Americas  |   42951.65|   1201.6372|   7136.110|   5465.510|
| Asia      |  113523.13|    331.0000|   7902.150|   2646.787|
| Europe    |   49357.19|    973.5332|  14469.476|  12081.749|
| Oceania   |   34435.37|  10039.5956|  18621.609|  17983.304|

This can be plotted specifically for the maximum and minimum GDP per continent.

``` r
GDPrange <- gapminder %>% group_by(continent) %>%
  summarize(maxGDP = max(gdpPercap), minGDP = min(gdpPercap))
ggplot(GDPrange, aes(maxGDP, minGDP)) + geom_boxplot(aes(color = continent))
```

![](Homework3_files/figure-markdown_github/unnamed-chunk-3-1.png)

### 2. Second task: Look at the spread of GDP per capita within the continents.

Like before, piping the `summarize` function is a quick way to get relevant ranges of the data within each continent, speically the *Mean* and *Median*.

``` r
gapminder %>% group_by(continent) %>%
  summarize(Mean = mean(gdpPercap),
            Median = median(gdpPercap)) %>%
 knitr::kable()
```

| continent |       Mean|     Median|
|:----------|----------:|----------:|
| Africa    |   2193.755|   1192.138|
| Americas  |   7136.110|   5465.510|
| Asia      |   7902.150|   2646.787|
| Europe    |  14469.476|  12081.749|
| Oceania   |  18621.609|  17983.304|

A violin plot can show the spread of countries by GDP per continent, which visually shows the edges of GDP values, or the maximum and minimum values. Asia and Oceania have interesting plots: Asia has great internal spread, with very large and very small GDPs, while Oceania has a small range, but even its lowest GDP is greater than most other countries.

``` r
Spread <- gapminder %>% group_by(country)
ggplot(Spread, aes(gdpPercap, gdpPercap)) + geom_violin(aes(alpha=0.5, color = continent))
```

    ## Warning: position_dodge requires non-overlapping x intervals

![](Homework3_files/figure-markdown_github/unnamed-chunk-5-1.png)

### 3. Third task: How is life expectancy changing over time on different continents?

In the case we dont know the range of years studied, we can use piping to extract the first and last years of the time-series per continent. In this case all share the same starting and ending year, but its good practice to run this analysis because wild time-series datasets never match. Here the first year is 1952 and the last is 2007.

``` r
gapminder %>% group_by(continent) %>% summarize(firstYear = min(year), lastYear = max(year)) %>%
 knitr::kable()
```

| continent |  firstYear|  lastYear|
|:----------|----------:|---------:|
| Africa    |       1952|      2007|
| Americas  |       1952|      2007|
| Asia      |       1952|      2007|
| Europe    |       1952|      2007|
| Oceania   |       1952|      2007|

Granted the spread observed before, it is possible that the average values within continents do not match the most frequent values. To observe the trends of both, it is possible to plot both the `Median` and the `Mean` over time per continent. Luckily, in this case both plots look very similar, with Asia expressing the greatest difference.

``` r
gapminder %>%  group_by(year,continent) %>% summarize(Median = median(lifeExp)) %>%
  ggplot(aes(y = Median, x = year, color = continent)) + geom_line()
```

![](Homework3_files/figure-markdown_github/unnamed-chunk-7-1.png)

``` r
gapminder %>%  group_by(year,continent) %>% summarize(Mean = mean(lifeExp)) %>%
  ggplot(aes(y = Mean, x = year, color = continent)) + geom_line()
```

![](Homework3_files/figure-markdown_github/unnamed-chunk-8-1.png)
