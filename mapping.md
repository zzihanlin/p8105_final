mapping
================
Kimberly Lopez
2024-12-03

``` r
library(tidyverse)
library(tigris)
library(sf)
library(usmap) # May need to install.packages to run this 
library(ggplot2)
library(dplyr)
```

Downloading state shape file Data Merging Asthma & temp & shapefile data

``` r
shape_files = usmap::us_map()

asthma_df = read_csv("data/asthma_data.csv")
```

    ## Rows: 559 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): state
    ## dbl (2): year_name, prevalence_percent
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
weather_df = read_csv("data/temp_data.csv")
```

    ## Rows: 2193 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): state, season
    ## dbl (2): year, avg_temp
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
merged_data=  
  shape_files|> 
  left_join(asthma_df, by = c("abbr" = "state")) |>
  mutate(state= abbr)

annual_weather =
  weather_df|>
  group_by(state, year) |>
  summarize(avg_temp_annual = mean(avg_temp, na.rm = TRUE), .groups = "drop")


asthma_weather= 
  asthma_df |>
  left_join(weather_df, by = c("state"))|>
  select(-year_name)
```

    ## Warning in left_join(asthma_df, weather_df, by = c("state")): Detected an unexpected many-to-many relationship between `x` and `y`.
    ## ℹ Row 1 of `x` matches multiple rows in `y`.
    ## ℹ Row 45 of `y` matches multiple rows in `x`.
    ## ℹ If a many-to-many relationship is expected, set `relationship =
    ##   "many-to-many"` to silence this warning.

``` r
final_df=
  shape_files |>
  mutate(state= abbr)|>
  left_join(asthma_weather, by = "state")
```

Mapping Prevalence data by state:

``` r
final_df|> 
  filter(year==2011)|>
  ggplot() +
  geom_sf(aes(fill = prevalence_percent), color = "white") +
  scale_fill_viridis_c(na.value = "grey90") + 
  theme_minimal() +
  labs(
    title = "Asthma Prevalence by State (2011)",
    fill = "Prevalence (%)"
  ) +
  theme(
    panel.grid = element_blank(), 
    axis.text = element_blank(),
    axis.title = element_blank(),
    axis.ticks = element_blank()
  )
```

![](mapping_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
final_df|> 
  filter(year==2012)|>
  ggplot() +
  geom_sf(aes(fill = prevalence_percent), color = "white") +
  scale_fill_viridis_c(na.value = "grey90") + 
  theme_minimal() +
  labs(
    title = "Asthma Prevalence by State (2012)",
    fill = "Prevalence (%)"
  ) +
  theme(
    panel.grid = element_blank(), 
    axis.text = element_blank(),
    axis.title = element_blank(),
    axis.ticks = element_blank()
  )
```

![](mapping_files/figure-gfm/unnamed-chunk-3-2.png)<!-- -->

``` r
final_df|> 
  filter(year==2013)|>
  ggplot() +
  geom_sf(aes(fill = prevalence_percent), color = "white") +
  scale_fill_viridis_c(na.value = "grey90") + 
  theme_minimal() +
  labs(
    title = "Asthma Prevalence by State (2013)",
    fill = "Prevalence (%)"
  ) +
  theme(
    panel.grid = element_blank(), 
    axis.text = element_blank(),
    axis.title = element_blank(),
    axis.ticks = element_blank()
  )
```

![](mapping_files/figure-gfm/unnamed-chunk-3-3.png)<!-- -->
