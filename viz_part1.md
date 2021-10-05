ggplot 1
================

Load in a dataset that we’ll use often.

``` r
# Load packages
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.4     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   2.0.1     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(ggridges)

# Set global options for embedding plots
knitr::opts_chunk$set(
      fig.width = 6,
      fig.asp = 0.6,
      out.width = "90%"
)

# Get data from NOAA and clean it up a bit
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2021-10-05 10:29:46 (7.602)

    ## file min/max dates: 1869-01-01 / 2021-10-31

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2021-10-05 10:29:52 (1.697)

    ## file min/max dates: 1965-01-01 / 2020-02-29

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2021-10-05 10:29:54 (0.912)

    ## file min/max dates: 1999-09-01 / 2021-09-30

``` r
weather_df
```

    ## # A tibble: 1,095 × 6
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

## Scatterplot

tmax vs tmin

``` r
ggp_tmax_tmin = 
      weather_df %>% 
      ggplot(aes(x = tmin, y = tmax)) + 
      geom_point()

ggp_tmax_tmin
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_part1_files/figure-gfm/unnamed-chunk-2-1.png" width="90%" />

## Let’s fancy it up

Add … color? lines? other stuff?

``` r
weather_df %>% 
      ggplot(aes(x = tmin, y = tmax, color = name)) + 
      geom_point(alpha = 0.3) +
      geom_smooth(se = FALSE) + 
      # First variable defines rows, second defines columns; do not want to separate rows, so use .
      facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_part1_files/figure-gfm/unnamed-chunk-3-1.png" width="90%" />

Let’s make one more scatterplot

``` r
weather_df %>% 
      ggplot(aes(x = date, y = tmax, size = prcp)) + 
      geom_point(alpha = 0.3) + 
      facet_grid(. ~ name) + 
      geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

<img src="viz_part1_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />

## Use data manipulation as part of this

``` r
weather_df %>% 
      filter(name == "CentralPark_NY") %>% 
      mutate(
            tmax = tmax*(9/5) + 32,
            tmin = tmin*(9/5) + 32
      ) %>% 
      ggplot(aes(x = tmin, y = tmax)) +
      geom_point()
```

<img src="viz_part1_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

## Stacking geoms

Which geoms do you want?

``` r
weather_df %>% 
      ggplot(aes(x = date, y = tmax, color = name)) +
      geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

<img src="viz_part1_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />

``` r
weather_df %>% 
      ggplot(aes(x = tmin, y = tmax)) +
      geom_hex()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_binhex).

<img src="viz_part1_files/figure-gfm/unnamed-chunk-6-2.png" width="90%" />

## Univariate plots

``` r
weather_df %>% 
      ggplot(aes(x = tmax, fill = name)) + 
      geom_histogram() + 
      facet_grid(. ~ name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

<img src="viz_part1_files/figure-gfm/unnamed-chunk-7-1.png" width="90%" />

Let’s try some other plots…

``` r
weather_df %>% ggplot(aes(x = tmax, fill = name)) + 
      geom_density(alpha = 0.3)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

<img src="viz_part1_files/figure-gfm/unnamed-chunk-8-1.png" width="90%" />

Still with `tmax` and `name`

``` r
weather_df %>% 
      ggplot(aes(x = name, y = tmax)) + 
      geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

<img src="viz_part1_files/figure-gfm/unnamed-chunk-9-1.png" width="90%" />

Some people like violin plots??? (Because doesn’t just give you
quantiles, but full distribution)

``` r
weather_df %>% 
      ggplot(aes(x = name, y = tmax)) + 
      geom_violin()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

<img src="viz_part1_files/figure-gfm/unnamed-chunk-10-1.png" width="90%" />

What about ridges…

``` r
weather_df %>% 
      ggplot(aes(x = tmax, y = name)) + 
      geom_density_ridges(alpha = 0.8, scale = 0.8)
```

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

<img src="viz_part1_files/figure-gfm/unnamed-chunk-11-1.png" width="90%" />

## Embedding plots

``` r
weather_df %>% 
      ggplot(aes(x = tmin, y = tmax, color = name)) + 
      geom_point(alpha = 0.3)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_part1_files/figure-gfm/unnamed-chunk-12-1.png" width="90%" />
