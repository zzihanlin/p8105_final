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
library(forecast)
```

Downloading state shape file Data

``` r
shape_files = usmap::us_map()

asthma_df = read_csv("data/asthma_data.csv")|>
  mutate(year= year_name)
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

Merging Asthma & temp & shapefile data

``` r
asthma_weather = 
  asthma_df |>
  left_join(weather_df, by = c("state", "year")) 


final_df =
  shape_files |>
  mutate(state = abbr) |>                           
  left_join(asthma_weather, by = "state") |>        
  drop_na()                                         
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

![](mapping_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

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

![](mapping_files/figure-gfm/unnamed-chunk-4-2.png)<!-- -->

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

![](mapping_files/figure-gfm/unnamed-chunk-4-3.png)<!-- -->

Do regions with higher asthma prevalence overlap with areas of lower
average temperature?

Does analyzing prevalence data stratified by time and temperature make a
difference?

- could stratify by creating temperature categories
- could then conduct if prevalence differs across temperatures
  categories

``` r
final_df= 
  final_df |>
  mutate(
    temp_category = case_when(
      avg_temp < 0 ~ "Cold",
      avg_temp >= 0 & avg_temp < 15 ~ "Mild",
      avg_temp >= 15 ~ "Warm"))|> 
  select(-c("abbr","fips", "year_name"))|>
  select(state,prevalence_percent,avg_temp, season,year,temp_category,everything())

final_df
```

    ## Simple feature collection with 2185 features and 7 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: -2590847 ymin: -2608148 xmax: 2523581 ymax: 731407.9
    ## Projected CRS: NAD27 / US National Atlas Equal Area
    ## # A tibble: 2,185 × 8
    ##    state prevalence_percent avg_temp season  year temp_category full  
    ##    <chr>              <dbl>    <dbl> <chr>  <dbl> <chr>         <chr> 
    ##  1 AK                   8.2    1.83  Fall    2011 Mild          Alaska
    ##  2 AK                   8.2    2.12  Spring  2011 Mild          Alaska
    ##  3 AK                   8.2   11.9   Summer  2011 Mild          Alaska
    ##  4 AK                   8.2   -5.34  Winter  2011 Cold          Alaska
    ##  5 AK                   9      1.17  Fall    2012 Mild          Alaska
    ##  6 AK                   9      1.49  Spring  2012 Mild          Alaska
    ##  7 AK                   9     11.8   Summer  2012 Mild          Alaska
    ##  8 AK                   9     -8.34  Winter  2012 Cold          Alaska
    ##  9 AK                   9.3    3.55  Fall    2013 Mild          Alaska
    ## 10 AK                   9.3    0.749 Spring  2013 Mild          Alaska
    ## # ℹ 2,175 more rows
    ## # ℹ 1 more variable: geom <MULTIPOLYGON [m]>

Does Correlation differ by state?

``` r
cor_results= 
  final_df |>
  group_by(state) |>
  summarise(
    cor_test = list(cor.test(avg_temp, prevalence_percent)),
    .groups = "drop")|>
  rowwise()|>
  mutate(
    corr=cor_test[["estimate"]],
    p_value= cor_test[["p.value"]])|>
  ungroup()|>
  select(-cor_test)

cor_results
```

    ## Simple feature collection with 50 features and 3 fields
    ## Geometry type: MULTIPOLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: -2590847 ymin: -2608148 xmax: 2523581 ymax: 731407.9
    ## Projected CRS: NAD27 / US National Atlas Equal Area
    ## # A tibble: 50 × 4
    ##    state                                                   geom     corr p_value
    ##    <chr>                                     <MULTIPOLYGON [m]>    <dbl>   <dbl>
    ##  1 AK    (((-2396847 -2547721, -2393297 -2546391, -2391552 -25…  0.0578    0.709
    ##  2 AL    (((1093777 -1378535, 1093269 -1374223, 1092965 -13414…  0.0106    0.945
    ##  3 AR    (((483065.2 -927788.2, 506062 -926263.3, 531512.5 -92…  0.0142    0.927
    ##  4 AZ    (((-1388676 -1254584, -1389181 -1251856, -1384522 -12…  0.0613    0.693
    ##  5 CA    (((-1719946 -1090033, -1709611 -1090026, -1700882 -11… -0.00830   0.957
    ##  6 CO    (((-789538.7 -678773.8, -789538.2 -678769.5, -781696.… -0.0228    0.883
    ##  7 CT    (((2161733 -83737.52, 2177182 -65221.22, 2168999 -581…  0.0308    0.843
    ##  8 DE    (((2042506 -284367.3, 2043078 -280000.3, 2044959 -275…  0.0219    0.888
    ##  9 FL    (((1855611 -2064809, 1860157 -2054372, 1867238 -20477… -0.0717    0.660
    ## 10 GA    (((1311336 -999180.8, 1323127 -997255.3, 1331180 -995… -0.00238   0.988
    ## # ℹ 40 more rows

Correlation is small across all states

Asthma Trend Across the Us over time:

``` r
aggregated_data=
  final_df|>
  group_by(year)|>
  summarise(avg_prevalence = mean(prevalence_percent, na.rm = TRUE))

ts_data= ts(aggregated_data[["avg_prevalence"]], start = min(aggregated_data[["year"]]), frequency = 1)

plot.ts(ts_data, 
        main = "Asthma Trend Across the US Over Time", 
        ylab = "Average Asthma Prevalence (%)",
        xlab = "Year")
```

![](mapping_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Does the effect of prevalence differ by state:

``` r
state_trends= 
  final_df |>
  group_by(state) |>
  summarise(
    model = list(lm(prevalence_percent ~ year)))


state_trends= 
  state_trends |>
  mutate(
    model_summary = map(model, broom::tidy)) |>
  unnest(model_summary)|>
  filter(term == "year")|>
  select(
    state, 
    slope = estimate, 
    intercept = NULL, 
    p_value = p.value)|>
  st_drop_geometry()|>
  knitr::kable(digits=5)

state_trends
```

| state |    slope | p_value |
|:------|---------:|--------:|
| AK    |  0.06182 | 0.00868 |
| AL    |  0.16455 | 0.00001 |
| AR    |  0.02909 | 0.25949 |
| AZ    |  0.06091 | 0.00049 |
| CA    |  0.02636 | 0.29868 |
| CO    |  0.16364 | 0.00000 |
| CT    |  0.09364 | 0.00000 |
| DE    |  0.04727 | 0.16551 |
| FL    | -0.05152 | 0.09681 |
| GA    |  0.00455 | 0.86590 |
| HI    | -0.06182 | 0.04633 |
| IA    |  0.08273 | 0.00179 |
| ID    |  0.10000 | 0.00000 |
| IL    |  0.03455 | 0.07010 |
| IN    |  0.02182 | 0.28358 |
| KS    |  0.18364 | 0.00000 |
| KY    |  0.05545 | 0.17836 |
| LA    |  0.19909 | 0.00000 |
| MA    | -0.00909 | 0.76559 |
| MD    |  0.04636 | 0.00314 |
| ME    |  0.01455 | 0.59673 |
| MI    |  0.09091 | 0.00002 |
| MN    |  0.11364 | 0.00000 |
| MO    | -0.05727 | 0.01216 |
| MS    |  0.22636 | 0.00000 |
| MT    |  0.11455 | 0.00003 |
| NC    |  0.03455 | 0.19492 |
| ND    |  0.03182 | 0.09917 |
| NE    |  0.10545 | 0.00000 |
| NH    |  0.14909 | 0.00023 |
| NJ    | -0.00400 | 0.87883 |
| NM    |  0.04292 | 0.17131 |
| NV    |  0.19000 | 0.00000 |
| NY    | -0.01636 | 0.41568 |
| OH    |  0.01727 | 0.47857 |
| OK    |  0.12545 | 0.00000 |
| OR    |  0.05091 | 0.01235 |
| PA    |  0.10091 | 0.00000 |
| RI    |  0.09364 | 0.00184 |
| SC    |  0.12182 | 0.00000 |
| SD    |  0.09909 | 0.00123 |
| TN    |  0.30727 | 0.00000 |
| TX    |  0.07182 | 0.00022 |
| UT    |  0.14455 | 0.00000 |
| VA    |  0.05636 | 0.00440 |
| VT    |  0.05636 | 0.01882 |
| WA    |  0.04818 | 0.00625 |
| WI    |  0.10182 | 0.00312 |
| WV    |  0.32091 | 0.00000 |
| WY    |  0.09182 | 0.00036 |
