viz\_and\_eda\_1
================
Lorraine Kwok
2019-09-25

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

## create a ggplot

``` r
## ggplot(dataset of interest, define what's on the x and y axes) + type of plot
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

alternate way of making this plot

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

saving initial plots

``` r
scatterplot =
  weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()

scatterplot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

adding color

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name), alpha = .4)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
## color is map feature we're interested in and name is the variable of interest
## alpha blending adds transparency to plot, 0 = transparent, 1 is not at all 
```

why do ‘aes’ positions matter?

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name), alpha = .4) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
## geom_smooth is often used for exploratory analysis
```

vs

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .4) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
## resulting plot has individual points and curves color-coded by the name 
```

## create facets

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .4) +
  geom_smooth(se = FALSE) +
  facet_grid(~name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
## facet_grid(~name) tells R you want name in the columns
```

## create plot to analyze seasonal trend

``` r
weather_df %>%
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point(aes(size = prcp), alpha = .5) +
  geom_smooth(size = 2, se = FALSE) +
  facet_grid(~name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
## size = prcp -> the size of the datapoints are determined by the precipitation data
```

## what happens when you remove geom\_point from code

``` r
weather_df %>%
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_smooth(size = 2, se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
## se = FALSE removes the standard errors from the plot
```

## 2d density plot

``` r
## install.packages("hexbin")
weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) +
  geom_hex() 
```

    ## Warning: Removed 15 rows containing non-finite values (stat_binhex).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## More kinds of plots

``` r
weather_df %>%
  ggplot(aes(x = tmax, fill = name)) +
  geom_histogram() +
  facet_grid(~name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](viz_and_eda_1_files/figure-gfm/univariate_plot-1.png)<!-- -->

``` r
## gives information on univariate distributio of tmax
## color = color of bar's outline
## fill = color of bar's fill
## position = "dodge" "unstacks" the bars
```

density plot - conveys similar information as histograms, but the plots
are a bit more smoothed out

``` r
weather_df %>%
  ggplot(aes(x = tmax, fill = name)) +
  geom_density(alpha = .3) 
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

boxplot

``` r
weather_df %>%
  ggplot(aes(x = name, y = tmax)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

``` r
## each location get its own boxplot 
```

violin plot

``` r
weather_df %>%
  ggplot(aes(x = name, y = tmax)) +
  geom_violin()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

ridge plots We’re looking at the density of tmax for Waterhole, Waikiki
and Central Park.

``` r
weather_df %>%
  ggplot(aes(x = tmax, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

## saving a plot

``` r
ggp_ridge_temp =
  weather_df %>%
  ggplot(aes(x = tmax, y = name)) +
  geom_density_ridges()

ggsave("ggplot_temp_ridge.pdf", ggp_ridge_temp)
```

    ## Saving 7 x 5 in image

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

``` r
## ggsave(file path name to be saved into, the name of the plot you want to save)
```

## embedding plots into R markdown

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name), alpha = .4) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name), alpha = .4) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_1_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->
