Visualization and EDA Part 2
================
Lorraine Kwok

## create the weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(c("USW00094728", "USC00519397", "USS0023B17S"),
                      var = c("PRCP", "TMIN", "TMAX"), 
                      date_min = "2017-01-01",
                      date_max = "2017-12-31") %>%
  mutate(
    name = recode(id, USW00094728 = "CentralPark_NY", 
                      USC00519397 = "Waikiki_HA",
                      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'crul':
    ##   method                 from
    ##   as.character.form_file httr

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## file path:          /Users/lorrainekwok/Library/Caches/rnoaa/ghcnd/USW00094728.dly

    ## file last updated:  2019-09-26 10:25:38

    ## file min/max dates: 1869-01-01 / 2019-09-30

    ## file path:          /Users/lorrainekwok/Library/Caches/rnoaa/ghcnd/USC00519397.dly

    ## file last updated:  2019-09-26 10:25:54

    ## file min/max dates: 1965-01-01 / 2019-09-30

    ## file path:          /Users/lorrainekwok/Library/Caches/rnoaa/ghcnd/USS0023B17S.dly

    ## file last updated:  2019-09-26 10:26:01

    ## file min/max dates: 1999-09-01 / 2019-09-30

``` r
weather_df
```

    ## # A tibble: 1,095 x 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # … with 1,085 more rows

## making new plots

start with an old plot

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

add labels:

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NDAA via rnoaa pacakge"
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

x axis tick marks etc

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NDAA via rnoaa pacakge"
  ) +
  scale_x_continuous(
    breaks = c(-15, -5, 20),
    labels = c("-15C", "-5C", "20C")
  ) +
  scale_y_continuous(
    trans = "sqrt"
  )
```

    ## Warning in self$trans$transform(x): NaNs produced

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 90 rows containing missing values (geom_point).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
# can also use scale_y_sqrt instead of scale_y_continuous(trans = "sqrt")
```

## setting colors

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NDAA via rnoaa pacakge"
  ) + 
  scale_color_hue(
    name = "Weather Station",
    h = c(50, 250) 
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
# name is a function to change the name of the legend
```

## viridis pacakage includes color palettes that are not built into base R

``` r
ggp_base = weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NDAA via rnoaa pacakge"
  ) + 
  viridis::scale_color_viridis(
    name = "Location", 
    discrete = TRUE
  )

ggp_base
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

## themes

are global things that tell you what the plot is often has to do with
organizational things overarching themes go first and any tweaks you
want to make go after

change legend position

``` r
# legend is at the bottom 
ggp_base + 
  theme(legend.position = "bottom") 
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

can change background theme

``` r
# black and white theme
ggp_base + 
  theme_bw() +
  theme(legend.position = "bottom") 
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
# minimum theme
ggp_base +
  theme_minimal() +
  theme(legend.position = "bottom") 
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

answering question about where the warning messages come from

``` r
weather_df %>%
  filter(is.na(tmin))
```

    ## # A tibble: 15 x 6
    ##    name       id          date        prcp  tmax  tmin
    ##    <chr>      <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 Waikiki_HA USC00519397 2017-04-17     5  28.3    NA
    ##  2 Waikiki_HA USC00519397 2017-05-09    NA  NA      NA
    ##  3 Waikiki_HA USC00519397 2017-05-26    NA  NA      NA
    ##  4 Waikiki_HA USC00519397 2017-07-19    NA  NA      NA
    ##  5 Waikiki_HA USC00519397 2017-10-07     0  31.1    NA
    ##  6 Waikiki_HA USC00519397 2017-10-09     0  28.9    NA
    ##  7 Waikiki_HA USC00519397 2017-10-10    10  31.7    NA
    ##  8 Waikiki_HA USC00519397 2017-10-12     0  31.1    NA
    ##  9 Waikiki_HA USC00519397 2017-10-13     0  31.1    NA
    ## 10 Waikiki_HA USC00519397 2017-10-16     5  30      NA
    ## 11 Waikiki_HA USC00519397 2017-10-18     0  29.4    NA
    ## 12 Waikiki_HA USC00519397 2017-10-20    13  30.6    NA
    ## 13 Waikiki_HA USC00519397 2017-10-21     0  30      NA
    ## 14 Waikiki_HA USC00519397 2017-10-22     0  30      NA
    ## 15 Waikiki_HA USC00519397 2017-12-22     0  26.7    NA

``` r
# ggplot is removing those 15 data points with missing x-values because it can't plot those data points 
```

can set theme at the very beginning to ensure that the plots always look
the same knitr::opts\_chunk$set( fig.width = 6, fig.asp = .6, out.width
= “90%” )

theme\_set(theme\_bw() + theme(legend.position = “bottom”))

## creating plots with more than one dataset

``` r
central_park =
  weather_df %>%
  filter(name == "CentralPark_NY")

waikiki =
  weather_df %>%
  filter(name == "Waikiki_HA")

# create scatterplot with waikiki dataset and added a line using central park dataset

ggplot(data = waikiki, aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_line(data = central_park)
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

(brief aside about colors)

``` r
# can specify color in geom_point
waikiki %>%
  ggplot(aes(x = date, y = tmax)) +
  geom_point(color = "blue")
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## Multi-panel plots

``` r
ggp_scatter =
  weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()

ggp_density = 
  weather_df %>%
  ggplot(aes(x = tmin)) +
  geom_density()

ggp_box = 
  weather_df %>% 
  ggplot(aes(x = name, y = tmax)) +
  geom_boxplot()

ggp_scatter
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

``` r
ggp_density
```

    ## Warning: Removed 15 rows containing non-finite values (stat_density).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-11-2.png)<!-- -->

``` r
ggp_box
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-11-3.png)<!-- -->

``` r
# shows the two plots side by side 
ggp_scatter + (ggp_density / ggp_box)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

    ## Warning: Removed 15 rows containing non-finite values (stat_density).

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-11-4.png)<!-- -->

## data manipulation

``` r
weather_df %>%
  mutate(
    name = factor(name),
    name = fct_relevel(name, "Waikiki_HA", "CentralPark_NY")
  ) %>%
  ggplot(aes(x = name, y = tmax, color = name)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

reorder instead of relevel reoder can put things in order according to a
different variable, so in this example instead of by location, it will
be according to tmax (Waterhole has the lowest tmax so it continues from
there)

``` r
weather_df %>%
  mutate(
    name = factor(name),
    name = fct_reorder(name, tmax)
  ) %>%
  ggplot(aes(x = name, y = tmax, color = name)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

## restructure the plot

``` r
weather_df %>%
  pivot_longer(
    tmax:tmin,
    names_to = "observation",
    values_to = "temperature"
    ) %>%
  ggplot(aes(x = temperature, fill = observation)) +
  geom_density(alpha = .5) +
  facet_grid(~name) +
  theme(legend.position = "bottom")
```

    ## Warning: Removed 18 rows containing non-finite values (stat_density).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

litters and pups

``` r
pup_data = 
  read_csv("./data/FAS_pups.csv", col_types = "ciiiii") %>%
  janitor::clean_names() %>%
  mutate(sex = recode(sex, `1` = "male", `2` = "female")) 

litter_data = 
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>%
  janitor::clean_names() %>%
  select(-pups_survive) %>%
  separate(group, into = c("dose", "day_of_tx"), sep = 3) %>%
  mutate(wt_gain = gd18_weight - gd0_weight,
         day_of_tx = as.numeric(day_of_tx))

fas_data = left_join(pup_data, litter_data, by = "litter_number") 
```

``` r
fas_data %>%
  ggplot(aes(x = dose, y = pd_ears)) +
  geom_violin() +
  facet_grid(day_of_tx ~.)
```

    ## Warning: Removed 18 rows containing non-finite values (stat_ydensity).

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

This is more of a data tidying problem and not a ggplot problem

``` r
fas_data %>% 
  select(sex, dose, day_of_tx, pd_ears:pd_walk) %>% 
  pivot_longer(
    pd_ears:pd_walk,
    names_to = "outcome", 
    values_to = "pn_day") %>% 
  drop_na() %>% 
  mutate(outcome = forcats::fct_reorder(outcome, day_of_tx, median)) %>% 
  ggplot(aes(x = dose, y = pn_day)) + 
  geom_violin() + 
  facet_grid(day_of_tx ~ outcome)
```

![](viz_and_eda_2_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

``` r
# facet on both day of treatment and outcome of interest 
```
