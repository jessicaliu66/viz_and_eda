Viz part 1
================

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(ggridges)

knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)
```

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"), #station ID
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") |>
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", # matching ID to station name
      USW00022534 = "Molokai_HI",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## using cached file: C:\Users\10507\AppData\Local/R/cache/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-09-28 10:22:00.028023 (8.541)

    ## file min/max dates: 1869-01-01 / 2023-09-30

    ## using cached file: C:\Users\10507\AppData\Local/R/cache/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2023-09-28 10:23:10.386177 (3.838)

    ## file min/max dates: 1949-10-01 / 2023-09-30

    ## using cached file: C:\Users\10507\AppData\Local/R/cache/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-09-28 10:23:27.579521 (0.996)

    ## file min/max dates: 1999-09-01 / 2023-09-30

Let’s make a plot!

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-3-1.png" width="90%" />

Pipes and stuff

``` r
weather_df |>
  filter(name == "CentralPark_NY") |>
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />

``` r
ggp_nyc_weather =
  weather_df |>
  filter(name == "CentralPark_NY") |>
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()

ggp_nyc_weather
```

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-4-2.png" width="90%" />

## Fancy plot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name),alpha = 0.3) + # alpha sets the transparency of points
  geom_smooth(se = FALSE) # add a smooth curve
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

Plot with facets

``` r
ggplot(weather_df, aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = 0.3) +
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name) 
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />

``` r
  # put everything other than variable name under each facet named by the variable
# facet_grid(name ~ .)
```

try assigning a specific color

``` r
weather_df |>
  filter(name != "CentralPark_NY") |>
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point(alpha = 0.7, size = 0.5)
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-7-1.png" width="90%" />

``` r
#  geom_point(color = "blue")
```

Let’s try a different plot. (temp is boring)

``` r
ggplot(weather_df, aes(x = date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp, alpha = 0.3)) + # assign point size by precipitation
  geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 19 rows containing missing values (`geom_point()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-8-1.png" width="90%" />

hex

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax)) +
  geom_hex()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_binhex()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-9-1.png" width="90%" />

line and points

``` r
weather_df |>
  filter(name == "Molokai_HI") |>
  ggplot(aes(x = date, y = tmax)) +
  geom_line(alpha = .5) +
  geom_point(size = .5)
```

    ## Warning: Removed 1 rows containing missing values (`geom_point()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-10-1.png" width="90%" />

## univariate plotting

histogram

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) +
#ggplot(weather_df, aes(x = tmax, color = name)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-11-1.png" width="90%" />

``` r
  #geom_histogram(position = "dodge")
```

Let’s use a density plot

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) +
  geom_density(alpha = 0.3, adjust = 0.75)
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_density()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-12-1.png" width="90%" />

Using boxplots!!

``` r
ggplot(weather_df, aes(y = tmax, x = name)) +
  geom_boxplot()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_boxplot()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-13-1.png" width="90%" />

Violin plot?

``` r
ggplot(weather_df, aes(y = tmax, x = name)) +
  geom_violin()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_ydensity()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-14-1.png" width="90%" />

ridge plot (density plot, but not overlapping)

``` r
ggplot(weather_df, aes(x = tmax, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.54

    ## Warning: Removed 17 rows containing non-finite values
    ## (`stat_density_ridges()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-15-1.png" width="90%" />

## saving and embedding plots

``` r
ggp_weather = 
    weather_df |>
    ggplot(aes(x = tmin, y = tmax)) +
  geom_point(size = .5)

ggp_nyc_weather
```

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-16-1.png" width="90%" />

``` r
ggsave("results/ggp_weather.pdf", ggp_weather)
```

    ## Saving 6 x 3.59 in image

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

``` r
#```{r, fig.width = 6, fig.height = 12}
ggp_weather
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="Viz_part_1_files/figure-gfm/unnamed-chunk-17-1.png" width="90%" />
