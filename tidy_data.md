Tidy Data
================

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.1      ✔ stringr 1.4.1 
    ## ✔ readr   2.1.2      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

# pivot longer

``` r
pulse_tidy_data = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>%
  janitor::clean_names() %>%
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit", 
    names_prefix = "bdi_score_",
    values_to = "bdi") %>%
  relocate(visit) %>%
  mutate(
    visit = replace(visit, visit == "bl", "00m"),
    visit = factor(visit)) %>%
  arrange(id, visit)
```

``` r
litters_tidy_data = 
read_csv("./data/FAS_litters.csv") %>%
  janitor::clean_names() %>%
  select(litter_number, ends_with("weight")) %>%
  pivot_longer(
    gd0_weight:gd18_weight,
    names_to = "gd", 
    values_to = "weight") %>%
  mutate(gd = recode(gd, "gd0_weight" = 0, "gd18_weight" = 18))
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

# pivot wider

``` r
analysis_result = tibble(
  group = c("treatment", "treatment", "placebo", "placebo"),
  time = c("pre", "post", "pre", "post"),
  mean = c(4, 8, 3.5, 4)
)

analysis_result_wide = pivot_wider(
  analysis_result, 
  names_from = "time", 
  values_from = "mean")
```

# binding rows

``` r
fellowship_ring = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "B3:D6") %>%
  mutate(movie = "fellowship_ring")

two_towers = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "F3:H6") %>%
  mutate(movie = "two_towers")

return_king = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "J3:L6") %>%
  mutate(movie = "return_king")
```

``` r
lotr_tidy = 
  bind_rows(fellowship_ring, two_towers, return_king) %>%
  janitor::clean_names() %>%
  pivot_longer(
    female:male,
    names_to = "gender", 
    values_to = "words") %>%
  mutate(race = str_to_lower(race)) %>% 
  select(movie, everything()) 

lotr_tidy
```

    ## # A tibble: 18 × 4
    ##    movie           race   gender words
    ##    <chr>           <chr>  <chr>  <dbl>
    ##  1 fellowship_ring elf    female  1229
    ##  2 fellowship_ring elf    male     971
    ##  3 fellowship_ring hobbit female    14
    ##  4 fellowship_ring hobbit male    3644
    ##  5 fellowship_ring man    female     0
    ##  6 fellowship_ring man    male    1995
    ##  7 two_towers      elf    female   331
    ##  8 two_towers      elf    male     513
    ##  9 two_towers      hobbit female     0
    ## 10 two_towers      hobbit male    2463
    ## 11 two_towers      man    female   401
    ## 12 two_towers      man    male    3589
    ## 13 return_king     elf    female   183
    ## 14 return_king     elf    male     510
    ## 15 return_king     hobbit female     2
    ## 16 return_king     hobbit male    2673
    ## 17 return_king     man    female   268
    ## 18 return_king     man    male    2459
