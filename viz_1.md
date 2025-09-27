Viz I
================

Import the weather data

``` r
data("weather_df")
```

Making our first plot :-)

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
# need to specify which geom I want to use
# + used for plots instead of piping
```

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
ggp_weather_scatterplot = 
  weather_df |>
    ggplot(aes(x = tmin, y = tmax)) +
    geom_point()

ggp_weather_scatterplot
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

Check that some rows are missing.

``` r
weather_df |>
  filter(is.na(tmax))
```

    ## # A tibble: 17 × 6
    ##    name         id          date        prcp  tmax  tmin
    ##    <chr>        <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 Molokai_HI   USW00022534 2022-05-31    NA    NA    NA
    ##  2 Waterhole_WA USS0023B17S 2021-03-09    NA    NA    NA
    ##  3 Waterhole_WA USS0023B17S 2021-12-07    51    NA    NA
    ##  4 Waterhole_WA USS0023B17S 2021-12-31     0    NA    NA
    ##  5 Waterhole_WA USS0023B17S 2022-02-03     0    NA    NA
    ##  6 Waterhole_WA USS0023B17S 2022-08-09    NA    NA    NA
    ##  7 Waterhole_WA USS0023B17S 2022-08-10    NA    NA    NA
    ##  8 Waterhole_WA USS0023B17S 2022-08-11    NA    NA    NA
    ##  9 Waterhole_WA USS0023B17S 2022-08-12    NA    NA    NA
    ## 10 Waterhole_WA USS0023B17S 2022-08-13    NA    NA    NA
    ## 11 Waterhole_WA USS0023B17S 2022-08-14    NA    NA    NA
    ## 12 Waterhole_WA USS0023B17S 2022-08-15    NA    NA    NA
    ## 13 Waterhole_WA USS0023B17S 2022-08-16    NA    NA    NA
    ## 14 Waterhole_WA USS0023B17S 2022-08-17    NA    NA    NA
    ## 15 Waterhole_WA USS0023B17S 2022-08-18    NA    NA    NA
    ## 16 Waterhole_WA USS0023B17S 2022-08-19    NA    NA    NA
    ## 17 Waterhole_WA USS0023B17S 2022-12-31    76    NA    NA

``` r
# produce summary of how much missingness is in the data
# naniar can also be used to explore and manage missing values
```

## Fancier scatterplots!

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.3) +
  geom_smooth(se = FALSE) # creates lines
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Where you define aesthetics can matter

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name, size = prcp), alpha = 0.3) +
  geom_smooth(se = FALSE) # only one smooth is included
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 19 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

use faceting real quick

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name), alpha = 0.3, size = 0.8) +
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name) # shows all three groups (names) as separate plots, column version
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name), alpha = 0.3, size = 0.8) +
  geom_smooth(se = FALSE) +
  facet_grid(name ~ .) # row version
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).
    ## Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-8-2.png)<!-- -->

Let’s make a somewhat more interesting scatterplot

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name, shape = name)) +
  geom_point(aes(size = prcp), alpha = 0.3) +
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 19 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
# different shapes are given to the names
```

Learning Assessment: Write a code chain that starts with weather_df;
focuses only on Central Park, converts temperatures to Fahrenheit, makes
a scatterplot of min vs. max temperature, and overlays a linear
regression line (using options in geom_smooth()).

LA plot:

``` r
weather_df |>
  filter(name == "CentralPark_NY") |>
  mutate(
    tmax_fahr = tmax * (9/5) + 32,
    tmin_fahr = tmin * (9/5) + 32
  ) |>
  ggplot(aes(x = tmin_fahr, y = tmax_fahr)) +
    geom_point() +
    geom_smooth(se = FALSE, method = "lm")
```

    ## `geom_smooth()` using formula = 'y ~ x'

![](viz_1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## Small things

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name, shape = name)) +
#  geom_point(alpha = 0.3) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

![](viz_1_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

``` r
# you don't need to have points in the plot

weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name, shape = name)) +
  geom_smooth(se = FALSE) +
  geom_point() 
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-11-2.png)<!-- -->

``` r
# here the points come after the smooth
```

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax)) +
  geom_hex()
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_binhex()`).

![](viz_1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax)) +
    geom_point(color = "#086bfd")
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

``` r
# can choose the color of the points
```

## Univariate plots

``` r
weather_df |>
  ggplot(aes(x = tmin)) +
  geom_histogram(color = "white", fill = "red")
```

    ## `stat_bin()` using `bins = 30`. Pick better value `binwidth`.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](viz_1_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

``` r
# color aesthetic is the founds, fill is the color of the bins
```

``` r
weather_df |>
  ggplot(aes(x = tmin, color = name)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value `binwidth`.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](viz_1_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

``` r
weather_df |>
  ggplot(aes(x = tmin, fill = name)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value `binwidth`.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](viz_1_files/figure-gfm/unnamed-chunk-15-2.png)<!-- -->

how would i fix this (better see the distribution of the data)? maybe
facet?

``` r
weather_df |>
  ggplot(aes(x = tmin, fill = name)) +
  geom_histogram() +
  facet_grid(name ~ .) # not overlapping anymore
```

    ## `stat_bin()` using `bins = 30`. Pick better value `binwidth`.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](viz_1_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

maybe a density plot?

``` r
weather_df |>
  ggplot(aes(x = tmin, fill = name)) +
  geom_density(alpha = 0.2) # made the color lighter to see the overlap better
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_density()`).

![](viz_1_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

``` r
weather_df |>
  ggplot(aes(x = name, y = tmin)) +
    geom_boxplot(aes(fill = name))
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_boxplot()`).

![](viz_1_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

violin plots

``` r
weather_df |>
  ggplot(aes(x = name, y = tmin, fill = name)) +
    geom_violin()
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

![](viz_1_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

``` r
# violin plot is a vertical density plot that is symmetrical
# good to tell you about the modality
```

ridge plot

``` r
weather_df |>
  ggplot(aes(x = tmin, y = name, fill = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.41

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_density_ridges()`).

![](viz_1_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

``` r
# good for showing density
```

LA univariate plots

Learning Assessment: Make plots that compare precipitation across
locations. Try a histogram, a density plot, a boxplot, a violin plot,
and a ridgeplot; use aesthetic mappings to make your figure readable.

``` r
weather_df |>
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = 0.2)
```

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_density()`).

![](viz_1_files/figure-gfm/unnamed-chunk-21-1.png)<!-- -->

``` r
weather_df |>
  ggplot(aes(x = prcp, fill = name)) +
  geom_histogram() +
  facet_grid(name  ~ .)
```

    ## `stat_bin()` using `bins = 30`. Pick better value `binwidth`.

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](viz_1_files/figure-gfm/unnamed-chunk-21-2.png)<!-- -->

``` r
weather_df |>
  filter(prcp > 5, prcp < 1000) |>
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = 0.2)
```

![](viz_1_files/figure-gfm/unnamed-chunk-21-3.png)<!-- -->

``` r
# zooming in on the data
```

## Saving and embedding plots

saving plots

``` r
ggp_weather_violin = 
  weather_df |>
  ggplot(aes(x = name, y = tmin, fill = name)) +
    geom_violin()

ggp_weather_violin
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

![](viz_1_files/figure-gfm/unnamed-chunk-22-1.png)<!-- -->

``` r
ggsave("plots/violin_plot.pdf", ggp_weather_violin,
       width = 8, height = 6)
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

``` r
# ggsave() takes plot name first, then plot you want to save, can also choose width and height
```

embedding plots

``` r
ggp_weather_violin
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

![](viz_1_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->
