EDA
================

Import the weather data

``` r
data("weather_df")

weather_df = 
  weather_df |> 
  mutate(month = floor_date(date, unit = "month")) # round down to month to compute month by month summaries
```

Make plots

``` r
weather_df |> 
  ggplot(aes(x = prcp)) + 
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value `binwidth`.

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

<img src="eda_files/figure-gfm/unnamed-chunk-2-1.png" width="90%" />

``` r
# there are some outliers
```

Check on extreme values

``` r
weather_df |> 
  filter(prcp > 1000)
```

    ## # A tibble: 3 × 7
    ##   name           id          date        prcp  tmax  tmin month     
    ##   <chr>          <chr>       <date>     <dbl> <dbl> <dbl> <date>    
    ## 1 CentralPark_NY USW00094728 2021-08-21  1130  27.8  22.8 2021-08-01
    ## 2 CentralPark_NY USW00094728 2021-09-01  1811  25.6  17.2 2021-09-01
    ## 3 Molokai_HI     USW00022534 2022-12-18  1120  23.3  18.9 2022-12-01

``` r
# can do a little bit digging to get the context of these extreme values/ outliers
```

Look at data again.

``` r
weather_df |> 
  filter(tmax >= 20, tmax <= 30) |> # subset tmax to 20-30
  ggplot(aes(x = tmin, y = tmax, color = name, shape = name)) + 
  geom_point()
```

<img src="eda_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />

``` r
# central park and molokai has grid distribution while waterhole is different (b/c they used different measurement processes)
```

## Add groups

``` r
weather_df |> 
  group_by(name, month)
```

    ## # A tibble: 2,190 × 7
    ## # Groups:   name, month [72]
    ##    name           id          date        prcp  tmax  tmin month     
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl> <date>    
    ##  1 CentralPark_NY USW00094728 2021-01-01   157   4.4   0.6 2021-01-01
    ##  2 CentralPark_NY USW00094728 2021-01-02    13  10.6   2.2 2021-01-01
    ##  3 CentralPark_NY USW00094728 2021-01-03    56   3.3   1.1 2021-01-01
    ##  4 CentralPark_NY USW00094728 2021-01-04     5   6.1   1.7 2021-01-01
    ##  5 CentralPark_NY USW00094728 2021-01-05     0   5.6   2.2 2021-01-01
    ##  6 CentralPark_NY USW00094728 2021-01-06     0   5     1.1 2021-01-01
    ##  7 CentralPark_NY USW00094728 2021-01-07     0   5    -1   2021-01-01
    ##  8 CentralPark_NY USW00094728 2021-01-08     0   2.8  -2.7 2021-01-01
    ##  9 CentralPark_NY USW00094728 2021-01-09     0   2.8  -4.3 2021-01-01
    ## 10 CentralPark_NY USW00094728 2021-01-10     0   5    -1.6 2021-01-01
    ## # ℹ 2,180 more rows

``` r
# we get 72 groups
# easy to miss grouping when it happened
```

Group and count things

``` r
weather_df |> 
  group_by(name) |> 
  summarize(
    n = n() # number of observations for each name group
  )
```

    ## # A tibble: 3 × 2
    ##   name               n
    ##   <chr>          <int>
    ## 1 CentralPark_NY   730
    ## 2 Molokai_HI       730
    ## 3 Waterhole_WA     730

``` r
weather_df |> 
  group_by(month) |> 
  summarize(
    n = n_distinct(date) # number of distinct dates for each month
  )
```

    ## # A tibble: 24 × 2
    ##    month          n
    ##    <date>     <int>
    ##  1 2021-01-01    31
    ##  2 2021-02-01    28
    ##  3 2021-03-01    31
    ##  4 2021-04-01    30
    ##  5 2021-05-01    31
    ##  6 2021-06-01    30
    ##  7 2021-07-01    31
    ##  8 2021-08-01    31
    ##  9 2021-09-01    30
    ## 10 2021-10-01    31
    ## # ℹ 14 more rows

You can count directly

``` r
weather_df |> 
  count(name) # counts number of observations for each name group
```

    ## # A tibble: 3 × 2
    ##   name               n
    ##   <chr>          <int>
    ## 1 CentralPark_NY   730
    ## 2 Molokai_HI       730
    ## 3 Waterhole_WA     730

## More interesting summaries

Compute some extra stuff

``` r
weather_df |> 
  group_by(name, month) |> 
  summarize(
    mean_tmax = mean(tmax, na.rm = TRUE),
    median_tmin = median(tmin, na.rm = TRUE), 
    sd_prcp = sd(prcp, na.rm = TRUE)
    # need to drop NAs or you will also be given NA for these summary statistics
  ) 
```

    ## `summarise()` has grouped output by 'name'. You can override using the
    ## `.groups` argument.

    ## # A tibble: 72 × 5
    ## # Groups:   name [3]
    ##    name           month      mean_tmax median_tmin sd_prcp
    ##    <chr>          <date>         <dbl>       <dbl>   <dbl>
    ##  1 CentralPark_NY 2021-01-01      4.27       -0.5     47.3
    ##  2 CentralPark_NY 2021-02-01      3.87       -1.85    98.1
    ##  3 CentralPark_NY 2021-03-01     12.3         5       71.3
    ##  4 CentralPark_NY 2021-04-01     17.6         8.05    52.4
    ##  5 CentralPark_NY 2021-05-01     22.1        11.1     74.7
    ##  6 CentralPark_NY 2021-06-01     28.1        18.0     43.3
    ##  7 CentralPark_NY 2021-07-01     28.4        21.1    151. 
    ##  8 CentralPark_NY 2021-08-01     28.8        22.2    236. 
    ##  9 CentralPark_NY 2021-09-01     24.8        17.5    333. 
    ## 10 CentralPark_NY 2021-10-01     19.9        13.9    151. 
    ## # ℹ 62 more rows

``` r
# here order doesn't matter
```

This is still a dataframe!!

``` r
weather_df |> 
  group_by(name, month) |> 
  summarize(
    mean_tmax = mean(tmax, na.rm = TRUE)
  ) |> 
  ggplot(aes(x = month, y = mean_tmax, color = name)) +
  geom_point() + 
  geom_line()
```

    ## `summarise()` has grouped output by 'name'. You can override using the
    ## `.groups` argument.

<img src="eda_files/figure-gfm/unnamed-chunk-10-1.png" width="90%" />

``` r
# mean t max for each month shown, there is a different line for each name
```

Sometimes format results more nicely.

``` r
weather_df |> 
  group_by(name, month) |> 
  summarize(
    mean_tmax = mean(tmax, na.rm = TRUE)
  ) |> 
  pivot_wider(
    names_from = name,
    values_from = mean_tmax
  ) |> # nicer way of seeing this table 
  knitr::kable(digits = 2) # create nicely formatted table, sets how many decimals to show
```

    ## `summarise()` has grouped output by 'name'. You can override using the
    ## `.groups` argument.

| month      | CentralPark_NY | Molokai_HI | Waterhole_WA |
|:-----------|---------------:|-----------:|-------------:|
| 2021-01-01 |           4.27 |      27.62 |         0.80 |
| 2021-02-01 |           3.87 |      26.37 |        -0.79 |
| 2021-03-01 |          12.29 |      25.86 |         2.62 |
| 2021-04-01 |          17.61 |      26.57 |         6.10 |
| 2021-05-01 |          22.08 |      28.58 |         8.20 |
| 2021-06-01 |          28.06 |      29.59 |        15.25 |
| 2021-07-01 |          28.35 |      29.99 |        17.34 |
| 2021-08-01 |          28.81 |      29.52 |        17.15 |
| 2021-09-01 |          24.79 |      29.67 |        12.65 |
| 2021-10-01 |          19.93 |      29.13 |         5.48 |
| 2021-11-01 |          11.54 |      28.85 |         3.53 |
| 2021-12-01 |           9.59 |      26.19 |        -2.10 |
| 2022-01-01 |           2.85 |      26.61 |         3.61 |
| 2022-02-01 |           7.65 |      26.83 |         2.99 |
| 2022-03-01 |          11.99 |      27.73 |         3.42 |
| 2022-04-01 |          15.81 |      27.72 |         2.46 |
| 2022-05-01 |          22.25 |      28.28 |         5.81 |
| 2022-06-01 |          26.09 |      29.16 |        11.13 |
| 2022-07-01 |          30.72 |      29.53 |        15.86 |
| 2022-08-01 |          30.50 |      30.70 |        18.83 |
| 2022-09-01 |          24.92 |      30.41 |        15.21 |
| 2022-10-01 |          17.43 |      29.22 |        11.88 |
| 2022-11-01 |          14.02 |      27.96 |         2.14 |
| 2022-12-01 |           6.76 |      27.35 |        -0.46 |

## mutate with groups

``` r
weather_df |> 
  group_by(name) |> 
  mutate(
    mean_tmax = mean(tmax, na.rm = TRUE), 
    center_tmax = tmax - mean_tmax # difference between tmax and the tmax mean, used to show difference between seasonal variation
  ) |> 
  ggplot(aes(x = date, y = center_tmax, color = name)) +
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

<img src="eda_files/figure-gfm/unnamed-chunk-12-1.png" width="90%" />

Look for cold days.

``` r
weather_df |> 
  group_by(name, month) |> 
  mutate(temp_rank = min_rank(desc(tmin))) |> 
  filter(temp_rank < 2)
```

    ## # A tibble: 98 × 8
    ## # Groups:   name, month [72]
    ##    name           id          date        prcp  tmax  tmin month      temp_rank
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl> <date>         <int>
    ##  1 CentralPark_NY USW00094728 2021-01-14     0   9.4   3.9 2021-01-01         1
    ##  2 CentralPark_NY USW00094728 2021-02-28    56   7.2   5   2021-02-01         1
    ##  3 CentralPark_NY USW00094728 2021-03-12     0  20    11.1 2021-03-01         1
    ##  4 CentralPark_NY USW00094728 2021-03-26    48  27.8  11.1 2021-03-01         1
    ##  5 CentralPark_NY USW00094728 2021-04-29     5  23.9  15   2021-04-01         1
    ##  6 CentralPark_NY USW00094728 2021-05-23     0  31.1  20   2021-05-01         1
    ##  7 CentralPark_NY USW00094728 2021-06-29     0  35    25.6 2021-06-01         1
    ##  8 CentralPark_NY USW00094728 2021-07-16     0  32.8  26.1 2021-07-01         1
    ##  9 CentralPark_NY USW00094728 2021-08-13     0  34.4  25.6 2021-08-01         1
    ## 10 CentralPark_NY USW00094728 2021-08-26     0  32.8  25.6 2021-08-01         1
    ## # ℹ 88 more rows

``` r
# shows the hottest day and its temperature for each month across names (locations) using tmin
# there is no max_rank
```

What about lags?

``` r
weather_df |> 
  group_by(name) |> 
  mutate(lagged_tmax = lag(tmax))
```

    ## # A tibble: 2,190 × 8
    ## # Groups:   name [3]
    ##    name           id         date        prcp  tmax  tmin month      lagged_tmax
    ##    <chr>          <chr>      <date>     <dbl> <dbl> <dbl> <date>           <dbl>
    ##  1 CentralPark_NY USW000947… 2021-01-01   157   4.4   0.6 2021-01-01        NA  
    ##  2 CentralPark_NY USW000947… 2021-01-02    13  10.6   2.2 2021-01-01         4.4
    ##  3 CentralPark_NY USW000947… 2021-01-03    56   3.3   1.1 2021-01-01        10.6
    ##  4 CentralPark_NY USW000947… 2021-01-04     5   6.1   1.7 2021-01-01         3.3
    ##  5 CentralPark_NY USW000947… 2021-01-05     0   5.6   2.2 2021-01-01         6.1
    ##  6 CentralPark_NY USW000947… 2021-01-06     0   5     1.1 2021-01-01         5.6
    ##  7 CentralPark_NY USW000947… 2021-01-07     0   5    -1   2021-01-01         5  
    ##  8 CentralPark_NY USW000947… 2021-01-08     0   2.8  -2.7 2021-01-01         5  
    ##  9 CentralPark_NY USW000947… 2021-01-09     0   2.8  -4.3 2021-01-01         2.8
    ## 10 CentralPark_NY USW000947… 2021-01-10     0   5    -1.6 2021-01-01         2.8
    ## # ℹ 2,180 more rows

``` r
# gives yesterday's value for tmax
# can tell you about day to day, used in public health
# order of the dataset matters!!!
# this just copies a value over and moves one down
# need to group by name
```

Use the variables you create

``` r
weather_df |> 
  group_by(name) |> 
  mutate(
    temp_change = tmax - lag(tmax) # temperature change between today and yesterday
  ) |> 
  summarize(
    sd_tmax_change = sd(temp_change, na.rm = TRUE),
    tmax_change_max = max(temp_change, na.rm = TRUE)
  )
```

    ## # A tibble: 3 × 3
    ##   name           sd_tmax_change tmax_change_max
    ##   <chr>                   <dbl>           <dbl>
    ## 1 CentralPark_NY           4.43            12.2
    ## 2 Molokai_HI               1.24             5.6
    ## 3 Waterhole_WA             3.04            11.1

``` r
# day to day temperature changes for each name (location), gives sd of temperature change and max temperature change
```

``` r
weather_df |> 
  group_by(name) |> 
  mutate(
    temp_change = tmax - lag(tmax), 
    change_rank = min_rank(desc(temp_change)) # use desc when looking for the maximum
  ) |>
  filter(
    change_rank < 2 # don't have to create change_rank variable, can just put it here
  )
```

    ## # A tibble: 4 × 9
    ## # Groups:   name [3]
    ##   name     id    date        prcp  tmax  tmin month      temp_change change_rank
    ##   <chr>    <chr> <date>     <dbl> <dbl> <dbl> <date>           <dbl>       <int>
    ## 1 Central… USW0… 2022-03-06    15  20     6.1 2022-03-01        12.2           1
    ## 2 Molokai… USW0… 2021-01-19     0  27.8  21.1 2021-01-01         5.6           1
    ## 3 Molokai… USW0… 2022-11-29     0  27.8  19.4 2022-11-01         5.6           1
    ## 4 Waterho… USS0… 2022-12-22    76   1.5 -17.2 2022-12-01        11.1           1

``` r
weather_df |> 
  group_by(name) |> 
  mutate(
    temp_change = tmax - lag(tmax), 
  ) |>
  filter(
    min_rank(desc(temp_change)) < 2
  )
```

    ## # A tibble: 4 × 8
    ## # Groups:   name [3]
    ##   name           id          date        prcp  tmax  tmin month      temp_change
    ##   <chr>          <chr>       <date>     <dbl> <dbl> <dbl> <date>           <dbl>
    ## 1 CentralPark_NY USW00094728 2022-03-06    15  20     6.1 2022-03-01        12.2
    ## 2 Molokai_HI     USW00022534 2021-01-19     0  27.8  21.1 2021-01-01         5.6
    ## 3 Molokai_HI     USW00022534 2022-11-29     0  27.8  19.4 2022-11-01         5.6
    ## 4 Waterhole_WA   USS0023B17S 2022-12-22    76   1.5 -17.2 2022-12-01        11.1

``` r
# gives maximum temperature change between current day and day before and the date asscociated with it
```

## Revisit PULSE

``` r
pulse_df = 
  haven::read_sas("data/public_pulse_data.sas7bdat") |> 
  janitor::clean_names() |> 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
  ) |> 
  mutate(visit = fct_inorder(visit))

pulse_df |> 
  ggplot(aes(x = visit, y = bdi)) + 
  geom_boxplot()
```

    ## Warning: Removed 879 rows containing non-finite outside the scale range
    ## (`stat_boxplot()`).

<img src="eda_files/figure-gfm/unnamed-chunk-18-1.png" width="90%" />

``` r
pulse_df |> 
  group_by(visit) |> 
  summarize(
    mean_bdi = mean(bdi, na.rm = TRUE),
    median_bdi = median(bdi, na.rm = TRUE)
  ) |> 
  knitr::kable(digits = 2)
```

| visit | mean_bdi | median_bdi |
|:------|---------:|-----------:|
| bl    |     7.99 |          6 |
| 01m   |     6.05 |          4 |
| 06m   |     5.67 |          4 |
| 12m   |     6.10 |          4 |
