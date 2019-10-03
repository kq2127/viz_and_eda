Visualization II
================
Kristal Quispe
10/3/2019

``` r
knitr::opts_chunk$set(echo = TRUE)

library(tidyverse)
```

    ## -- Attaching packages -------------- tidyverse 1.2.1 --

    ## v ggplot2 3.2.1     v purrr   0.3.2
    ## v tibble  2.1.3     v dplyr   0.8.3
    ## v tidyr   1.0.0     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.4.0

    ## -- Conflicts ----------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(ggridges)
```

    ## 
    ## Attaching package: 'ggridges'

    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     scale_discrete_manual

## Create Weather Data

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

    ## file path:          C:\Users\kriqu\AppData\Local\rnoaa\rnoaa\Cache/ghcnd/USW00094728.dly

    ## file last updated:  2019-10-02 16:18:47

    ## file min/max dates: 1869-01-01 / 2019-09-30

    ## file path:          C:\Users\kriqu\AppData\Local\rnoaa\rnoaa\Cache/ghcnd/USC00519397.dly

    ## file last updated:  2019-10-02 16:19:05

    ## file min/max dates: 1965-01-01 / 2019-09-30

    ## file path:          C:\Users\kriqu\AppData\Local\rnoaa\rnoaa\Cache/ghcnd/USS0023B17S.dly

    ## file last updated:  2019-10-02 16:19:13

    ## file min/max dates: 1999-09-01 / 2019-09-30

## Making new plots

start with an old one

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha =.5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_ii_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

So now we are going to add labels:

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha =.5) +
  labs(
    title = "Temperture plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NOAA via rnoaa package"
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_ii_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

lets play around with scales: x axis tick marks etc

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha =.5) +
  labs(
    title = "Temperture plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NOAA via rnoaa package"
  ) +
  scale_x_continuous(
    breaks = c(-15, -5, 20),
    labels = c("-15C","-5C", "20C" )
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_ii_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

If you need a log or square root transfromation

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha =.5) +
  labs(
    title = "Temperture plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NOAA via rnoaa package"
  ) +
  scale_x_continuous(
    breaks = c(-15, -5, 20),
    labels = c("-15C","-5C", "20C" )
  ) +
  scale_y_continuous(
    trans ="sqrt"
  )
```

    ## Warning in self$trans$transform(x): NaNs produced

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 90 rows containing missing values (geom_point).

![](visualization_ii_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
#or you can do...
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha =.5) +
  labs(
    title = "Temperture plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NOAA via rnoaa package"
  ) +
  scale_x_continuous(
    breaks = c(-15, -5, 20),
    labels = c("-15C","-5C", "20C" )
  ) +
  scale_y_sqrt()
```

    ## Warning in self$trans$transform(x): NaNs produced

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 90 rows containing missing values (geom_point).

![](visualization_ii_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

## Colors next

``` r
## to change the default colors ggplot sets use a scale color function...
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha =.5) +
  labs(
    title = "Temperture plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NOAA via rnoaa package"
  ) +
  scale_color_hue(
    name = "Location",
    h = c(50, 250)
    #name is the key legend name, not the variable name
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_ii_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

viridis package… gives us more color options

``` r
ggp_base =
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha =.5) +
  labs(
    title = "Temperture plot",
    x = "Minimum Temp (C)",
    y = "Maximum Temp (C)",
    caption = "Data from NOAA via rnoaa package"
  ) +
  viridis::scale_color_viridis(
    name = "Location", 
    discrete = TRUE ##variable here is a discrete variable, need to specify this. 
  )

ggp_base
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_ii_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

## themes

``` r
## theme has to do with general organization functions, here i am moving legend to the bottom
ggp_base +
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_ii_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
#If you want a black and white back drop
ggp_base +
  theme_bw ()+
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_ii_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
#If you want a minimal theme back drop, aka get rid of the black out line in grpah
ggp_base +
  theme_minimal ()+
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_ii_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
#theme minimal has to happen before them legened position, b/c theme minimal is applying a specfic lengend. Over arching themes go first aka like theme minimal, and any tweeks, like them lengend position go after. 
```

``` r
#If you want to drop the legend completely
ggp_base +
  theme_minimal ()+
  theme(legend.position = "none")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](visualization_ii_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->