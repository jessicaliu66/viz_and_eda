Viz part 2
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
library(patchwork)

knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)

#theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)

scale_colour_discrete = scale_colour_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"), #station ID
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2022-01-01",
    date_max = "2023-12-31") |>
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

This results in a df with 1914 observations on six variables.

## Same plot from last time

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5)+
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp",
    color = "Location",
    caption = "Max vs min daily temp  in three locations, data from rnoaa"
  )
```

    ## Warning: Removed 30 rows containing missing values (`geom_point()`).

<img src="Viz_part_2_files/figure-gfm/plot labels and names-1.png" width="90%" />

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5)+
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp",
    color = "Location",
    caption = "Max vs min daily temp  in three locations, data from rnoaa"
  ) +
  scale_x_continuous( # x axis
    breaks = c(-15, 0, 15), 
    labels = c("-15 C", "0", "15")
  ) +
  scale_y_continuous( # y axis
    position = "right" # put y axis on right hand side
    #,trans = "sqrt", #usually do in df, not when plotting
    #limits = c(0, 30) #usually do by filtering before
  )
```

    ## Warning: Removed 30 rows containing missing values (`geom_point()`).

<img src="Viz_part_2_files/figure-gfm/x and y axis-1.png" width="90%" />

what about colors …

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5)+
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp",
    color = "Location",
    caption = "Max vs min daily temp  in three locations, data from rnoaa"
  ) +
  viridis::scale_color_viridis(discrete = TRUE)
```

    ## Warning: Removed 30 rows containing missing values (`geom_point()`).

<img src="Viz_part_2_files/figure-gfm/scale_color_hue() and scale_color_viridis()-1.png" width="90%" />

``` r
  # use the viridis color scale instead of defalut
```

## Themes

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5)+
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp",
    color = "Location",
    caption = "Max vs min daily temp  in three locations, data from rnoaa"
  ) +
  viridis::scale_color_viridis(discrete = TRUE) +
  theme_bw() + # transfer background from grey to black-and-white
  #theme_classic() + # no grid-lines
  #theme_minimal() + # no out lines
  theme(legend.position = "bottom") # put legend at the bottom
```

    ## Warning: Removed 30 rows containing missing values (`geom_point()`).

<img src="Viz_part_2_files/figure-gfm/theme-1.png" width="90%" />

## data argument

``` r
weather_df |>
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 30 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 30 rows containing missing values (`geom_point()`).

<img src="Viz_part_2_files/figure-gfm/unnamed-chunk-3-1.png" width="90%" />

``` r
nyc_weather_df =
  weather_df |>
  filter(name == "CentralPark_NY")

hawaii_weather_df =
  weather_df |>
  filter(name == "Molokai_HI")

ggplot(nyc_weather_df, aes(x = date, y= tmax, color = name)) +
  geom_point() +
  geom_line(data = hawaii_weather_df)
```

    ## Warning: Removed 5 rows containing missing values (`geom_point()`).

    ## Warning: Removed 5 rows containing missing values (`geom_line()`).

<img src="Viz_part_2_files/figure-gfm/unnamed-chunk-3-2.png" width="90%" />

## `patchwork`

``` r
weather_df |>
  ggplot(aes(x = date, y= tmax, color = name)) +
  geom_point() +
  facet_grid(. ~ name) # separate the plot under their names
```

    ## Warning: Removed 30 rows containing missing values (`geom_point()`).

<img src="Viz_part_2_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />

``` r
ggp_temp_scatter = 
  weather_df |>
  ggplot(aes(x = tmin, y= tmax, color = name)) +
  geom_point(alpha = .5) +
  theme(legend.position = "none")

ggp_prcp_density = 
  weather_df |>
  filter(prcp > 25) |>
  ggplot(aes(x = prcp, fill = name)) +
  geom_density() +
  theme(legend.position = "none")

ggp_tmax_date =
  weather_df |>
  ggplot(aes(x = date, y= tmax, color = name)) +
  geom_point() +
  geom_smooth(se = FALSE) +
  theme(legend.position = "bottom")

(ggp_temp_scatter + ggp_prcp_density) / ggp_tmax_date
```

    ## Warning: Removed 30 rows containing missing values (`geom_point()`).

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 30 rows containing non-finite values (`stat_smooth()`).
    ## Removed 30 rows containing missing values (`geom_point()`).

<img src="Viz_part_2_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

## data manipulation

``` r
weather_df |>
  mutate(
    name = fct_relevel(name, c("Molokai_HI", "CentralPark_NY", "Waterhole_WA")) # assign new order to the name
  ) |>
  ggplot(aes(x = name, y= tmax)) +
  geom_boxplot() 
```

    ## Warning: Removed 30 rows containing non-finite values (`stat_boxplot()`).

<img src="Viz_part_2_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />

``` r
weather_df |>
  mutate(
    name = fct_reorder(name, tmax) # reorder name according to tmax
  ) |>
  ggplot(aes(x = name, y= tmax, fill = name)) +
  geom_violin() 
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `name = fct_reorder(name, tmax)`.
    ## Caused by warning:
    ## ! `fct_reorder()` removing 30 missing values.
    ## ℹ Use `.na_rm = TRUE` to silence this message.
    ## ℹ Use `.na_rm = FALSE` to preserve NAs.

    ## Warning: Removed 30 rows containing non-finite values (`stat_ydensity()`).

<img src="Viz_part_2_files/figure-gfm/unnamed-chunk-6-2.png" width="90%" />

## complicated FAS plot

``` r
litters_df =
  read_csv("data/FAS_litters.csv") |>
  janitor::clean_names() |>
  separate(group, into = c("dose", "day_of_tx"), sep = 3)
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
pups_df =
  read_csv("data/FAS_pups.csv") |>
  janitor::clean_names() 
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
fas_df = 
  left_join(pups_df, litters_df, by = "litter_number")

fas_df |>
  select(dose, day_of_tx, starts_with("pd")) |>
  pivot_longer(
    pd_ears:pd_walk,
    names_to = "outcome",
    values_to = "pn_day"
  ) |>
  drop_na() |>
  mutate(outcome = fct_reorder(outcome, pn_day)) |>
  ggplot(aes(x = dose,y = pn_day)) +
  geom_violin() +
  facet_grid(day_of_tx ~ outcome)
```

<img src="Viz_part_2_files/figure-gfm/unnamed-chunk-7-1.png" width="90%" />
