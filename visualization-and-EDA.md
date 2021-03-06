ggplot
================

I’m an R Markdown document!

``` r
library(tidyverse)
```

    ## ─ Attaching packages ──────────────────── tidyverse 1.3.1 ─

    ## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.4     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   2.0.1     ✓ forcats 0.5.1

    ## ─ Conflicts ───────────────────── tidyverse_conflicts() ─
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(viridis)
```

    ## 载入需要的程辑包：viridisLite

``` r
library(patchwork)
library(ggridges)
knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp =.6,
  out.width = "90%"
)
```

# 10/5 Visualization 1

load in a dataset that we will use often

``` r
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

    ## date created (size, mb): 2021-09-09 10:44:54 (7.599)

    ## file min/max dates: 1869-01-01 / 2021-09-30

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2021-09-09 10:44:58 (1.697)

    ## file min/max dates: 1965-01-01 / 2020-02-29

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2021-09-09 10:45:00 (0.909)

    ## file min/max dates: 1999-09-01 / 2021-09-30

## Scatterplot

tmax vs tmin

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-3-1.png" width="90%" />

you can save ggplot

``` r
ggp_tmax_tin <- 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()

ggp_tmax_tin
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />

``` r
##ggsave()
```

## let’s fancy it up

add color? lines? other stuff?

``` r
## what in ggplot apply to everything
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax,color = name )) +
  geom_point(alpha = 0.3) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

``` r
## when aes in geom_point, it will only work for point

weather_df %>% 
  ggplot(aes(x = tmin, y = tmax )) +
  ## we can use null to make it not apply
  ## geom_point(aes(color = NULL) ,alpha = 0.3)
  geom_point(aes(color = name) ,alpha = 0.3) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-5-2.png" width="90%" />

``` r
## when aes in geom_point, it will only work for point
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name )) +
  geom_point(alpha = 0.3) +
  geom_smooth(se = FALSE) +
  facet_grid(.~name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-5-3.png" width="90%" />
let’s make one more scatterplot

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax ,size = prcp)) +
  geom_point(alpha = .3) +
  facet_grid(.~name) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />

## use data manipulation as part of this

``` r
weather_df %>% 
  filter(name == "CentralPark_NY") %>% 
  mutate(
    tmax = tmax * (9 / 5) + 32,
    tmin = tmin * (9 / 5) + 32
  ) %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-7-1.png" width="90%" />

which geoms do you want

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name))+
  geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-8-1.png" width="90%" />

``` r
##hexbin and geom_hex which can be used for a alot of data
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax))+
  geom_hex()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_binhex).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-8-2.png" width="90%" />

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax))+
  geom_bin_2d()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_bin2d).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-8-3.png" width="90%" />

## UNIvariate plots

``` r
## fill = name to show the resource
weather_df %>% 
  ggplot(aes( x = tmax, fill = name)) +
  geom_histogram() +
  facet_grid(.~name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-9-1.png" width="90%" />

Let’s try some other plots…

``` r
weather_df %>% 
  ggplot(aes(x = tmax, fill = name))+
  geom_density(alpha = .3)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-10-1.png" width="90%" />

still with tmax and tmin

``` r
weather_df %>% 
  ggplot(aes(x = name , y = tmax)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-11-1.png" width="90%" />

some people like violin pilot

``` r
weather_df %>% 
  ggplot(aes(x = name , y = tmax , color =name)) +
  geom_violin()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-12-1.png" width="90%" />

What about rigdes…

``` r
weather_df %>% 
  ggplot(aes(x = tmax, y = name))+
  geom_density_ridges(alpha =.8, scale = .8)
```

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-13-1.png" width="90%" />

## Embedding plots

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.3)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-14-1.png" width="90%" />

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.3)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-15-1.png" width="90%" />

# 10/7

## start with a familiar one

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name))+
  geom_point(alpha = .3)+
  labs(
    title = "Temperature at three stations",
    x = "Minimum daily temperature(c)",
    y = "Maximum daily temperature(c)",
    caption = "Data from the rnoaa package with three stations"
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-16-1.png" width="90%" />

## Scales

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .3) +
  labs(
    title = "Temperature at three stations",
    x = "Minimum daily temperature(c)",
    y = "Maximum daily temperature(c)",
    caption = "Data from the rnoaa package"
  )+
  scale_x_continuous(
    breaks = c(-15,0,15),
    labels = c("-15","0","15")
  ) +
  ##scale_y_continuous(
    ##breaks = c(10,20,30),
    ##labels = c("10","20","30")
  #)
  scale_y_continuous(
    trans = "sqrt",
    position = "right"
  )
```

    ## Warning in self$trans$transform(x): 产生了NaNs

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 90 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-17-1.png" width="90%" />

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .3) +
  labs(
    title = "Temperature at three stations",
    x = "Minimum daily temperature(c)",
    y = "Maximum daily temperature(c)",
    caption = "Data from the rnoaa package"
  ) +
  scale_color_hue(
    name = "Location", 
    h = c(100, 300))+
  scale_color_viridis_d()
```

    ## Scale for 'colour' is already present. Adding another scale for 'colour',
    ## which will replace the existing scale.

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-18-1.png" width="90%" />

## Themes

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .3) +
  labs(
    title = "Temperature at three stations",
    x = "Minimum daily temperature(c)",
    y = "Maximum daily temperature(c)",
    caption = "Data from the rnoaa package with three stations"
  ) +
  scale_color_viridis_d() +
  # change the position
  # it is important to make sure the postion of theme command
  #theme_bw() + 
  #theme_classic() +
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-19-1.png" width="90%" />

## Setting for the whole file

``` r
library(tidyverse)

knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)

theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)

scale_colour_discrete = scale_colour_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

## `data` in geoms

``` r
central_park <- 
  weather_df %>% 
  filter(name == "CentralPark_NY")

waikiki = 
  weather_df %>% 
  filter(name == "Waikiki_HA")

#point from waikiki and line from centralpark
waikiki %>% 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_line(data = central_park)
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-21-1.png" width="90%" />

## `patchwork`

``` r
ggp_tmax_tin <- 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name))+
  geom_point(alpha = .3)+
  geom_smooth()


ggp_prcp_dens <-
  weather_df %>% 
  filter(prcp > 0) %>% 
  ggplot(aes(x = prcp, fill = name)) + 
  geom_density(alpha = .3)

tmax_date_p <- 
  weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point(alpha = .5) +
  geom_smooth(se = FALSE) + 
  theme(legend.position = "bottom")

## + for the same row ,and / for new row
ggp_tmax_tin / (ggp_prcp_dens + tmax_date_p)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-22-1.png" width="90%" />

## data manipulation

quick example on factors

``` r
weather_df %>% 
  mutate(name = fct_reorder(name, tmax)
  ) %>% 
  ggplot(aes(x = name, y = tmax))+
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-23-1.png" width="90%" />

What about tmax and tmin 。…

``` r
weather_df %>% 
  pivot_longer(
    tmax:tmin,
    names_to = "obs",
    values_to = "temperature"
  ) %>% 
  ggplot(aes(x = temperature, fill =obs))+
  geom_density(alpha = .3)+
  facet_grid(.~name)
```

    ## Warning: Removed 18 rows containing non-finite values (stat_density).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-24-1.png" width="90%" />

``` r
pulse_df <- 
  haven::read_sas("data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    values_to = "bdi",
    names_prefix = "bdi_score_"
  ) %>% 
  mutate(
    visit = recode(visit, "bl" = "00m")
    )

pulse_df %>% 
  ggplot(aes(x = visit, y = bdi)) +
  geom_boxplot()
```

    ## Warning: Removed 879 rows containing non-finite values (stat_boxplot).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-25-1.png" width="90%" />

``` r
pulse_df %>% 
  ggplot(aes(x = visit, y = bdi)) +
  geom_point(size = .2) +
  geom_line(aes(group = id),alpha = .3)
```

    ## Warning: Removed 879 rows containing missing values (geom_point).

    ## Warning: Removed 515 row(s) containing missing values (geom_path).

<img src="visualization-and-EDA_files/figure-gfm/unnamed-chunk-25-2.png" width="90%" />
