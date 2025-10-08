MATH 534 HW 4
================
David Zheng
2025-09-27

- [Domain problem formulation](#domain-problem-formulation)
- [Data source overview](#data-source-overview)
- [a) Step 1: Review background information](#sec-bg-info)
  - [Data dictionary](#data-dictionary)
- [Step 2: Loading in the data](#step-2-loading-in-the-data)
- [Step 3: Examine the data and create action
  items](#step-3-examine-the-data-and-create-action-items)
  - [Finding invalid values](#finding-invalid-values)
  - [Assessing Column names](#assessing-column-names)
  - [Assessing variable type](#assessing-variable-type)
  - [Assessing data completeness](#assessing-data-completeness)
- [Step 4: Clean and pre-process the
  data](#step-4-clean-and-pre-process-the-data)

## Domain problem formulation

## Data source overview

## a) Step 1: Review background information

Two datasets are used:

IMF public debt data (debt.xls) provides annual government debt as a
percentage of GDP from 1800 to 2015 for 194 countries/regions.

World Bank GDP growth data (growth.csv) provides annual real GDP growth
rates (percent change) from 1960 to 2021 for 266 countries/regions.

Each observation represents a country/region-year pair. Because the IMF
data begins much earlier than the World Bank data, we restrict both
datasets to 1960 onward to ensure overlapping years.

### Data dictionary

Variable Description country Country name year Calendar year
debt_pct_gdp Central-government debt as % of GDP growth_pct_gdp Annual
GDP growth (%)

## Step 2: Loading in the data

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.2     ✔ tibble    3.3.0
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ## ✔ purrr     1.1.0     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(readxl)
library(janitor)
```

    ## 
    ## Attaching package: 'janitor'
    ## 
    ## The following objects are masked from 'package:stats':
    ## 
    ##     chisq.test, fisher.test

``` r
# Load datasets
debt_raw   <- read_excel("/Users/dzheng46/Documents/MATH_534_Materials/debt.xls")
growth_raw <- read_csv(
  "/Users/dzheng46/Documents/MATH_534_Materials/growth.csv",
  skip = 3,                    # start at the header row that begins "Country Name"
  show_col_types = FALSE
)
```

    ## New names:
    ## • `` -> `...67`

``` r
debt_raw
```

    ## # A tibble: 197 × 217
    ##    `DEBT (% of GDP)`   `1800`  `1801`  `1802` `1803` `1804` `1805` `1806` `1807`
    ##    <chr>               <chr>   <chr>   <chr>  <chr>  <chr>  <chr>  <chr>  <chr> 
    ##  1 <NA>                <NA>    <NA>    <NA>   <NA>   <NA>   <NA>   <NA>   <NA>  
    ##  2 Afghanistan         no data no data no da… no da… no da… no da… no da… no da…
    ##  3 Albania             no data no data no da… no da… no da… no da… no da… no da…
    ##  4 Algeria             no data no data no da… no da… no da… no da… no da… no da…
    ##  5 Angola              no data no data no da… no da… no da… no da… no da… no da…
    ##  6 Anguilla            no data no data no da… no da… no da… no da… no da… no da…
    ##  7 Antigua and Barbuda no data no data no da… no da… no da… no da… no da… no da…
    ##  8 Argentina           no data no data no da… no da… no da… no da… no da… no da…
    ##  9 Armenia             no data no data no da… no da… no da… no da… no da… no da…
    ## 10 Australia           no data no data no da… no da… no da… no da… no da… no da…
    ## # ℹ 187 more rows
    ## # ℹ 208 more variables: `1808` <chr>, `1809` <chr>, `1810` <chr>, `1811` <chr>,
    ## #   `1812` <chr>, `1813` <chr>, `1814` <chr>, `1815` <chr>, `1816` <chr>,
    ## #   `1817` <chr>, `1818` <chr>, `1819` <chr>, `1820` <chr>, `1821` <chr>,
    ## #   `1822` <chr>, `1823` <chr>, `1824` <chr>, `1825` <chr>, `1826` <chr>,
    ## #   `1827` <chr>, `1828` <chr>, `1829` <chr>, `1830` <chr>, `1831` <chr>,
    ## #   `1832` <chr>, `1833` <chr>, `1834` <chr>, `1835` <chr>, `1836` <chr>, …

``` r
growth_raw
```

    ## # A tibble: 266 × 67
    ##    `Country Name` `Country Code` `Indicator Name` `Indicator Code` `1960` `1961`
    ##    <chr>          <chr>          <chr>            <chr>            <lgl>   <dbl>
    ##  1 Aruba          ABW            GDP growth (ann… NY.GDP.MKTP.KD.… NA     NA    
    ##  2 Africa Easter… AFE            GDP growth (ann… NY.GDP.MKTP.KD.… NA      0.240
    ##  3 Afghanistan    AFG            GDP growth (ann… NY.GDP.MKTP.KD.… NA     NA    
    ##  4 Africa Wester… AFW            GDP growth (ann… NY.GDP.MKTP.KD.… NA      1.85 
    ##  5 Angola         AGO            GDP growth (ann… NY.GDP.MKTP.KD.… NA     NA    
    ##  6 Albania        ALB            GDP growth (ann… NY.GDP.MKTP.KD.… NA     NA    
    ##  7 Andorra        AND            GDP growth (ann… NY.GDP.MKTP.KD.… NA     NA    
    ##  8 Arab World     ARB            GDP growth (ann… NY.GDP.MKTP.KD.… NA     NA    
    ##  9 United Arab E… ARE            GDP growth (ann… NY.GDP.MKTP.KD.… NA     NA    
    ## 10 Argentina      ARG            GDP growth (ann… NY.GDP.MKTP.KD.… NA      5.43 
    ## # ℹ 256 more rows
    ## # ℹ 61 more variables: `1962` <dbl>, `1963` <dbl>, `1964` <dbl>, `1965` <dbl>,
    ## #   `1966` <dbl>, `1967` <dbl>, `1968` <dbl>, `1969` <dbl>, `1970` <dbl>,
    ## #   `1971` <dbl>, `1972` <dbl>, `1973` <dbl>, `1974` <dbl>, `1975` <dbl>,
    ## #   `1976` <dbl>, `1977` <dbl>, `1978` <dbl>, `1979` <dbl>, `1980` <dbl>,
    ## #   `1981` <dbl>, `1982` <dbl>, `1983` <dbl>, `1984` <dbl>, `1985` <dbl>,
    ## #   `1986` <dbl>, `1987` <dbl>, `1988` <dbl>, `1989` <dbl>, `1990` <dbl>, …

## Step 3: Examine the data and create action items

After examining the data, there were several issues found.

### Finding invalid values

There were missing GDP growth or debt values. The action item to take
against this is to keep those missing values as NA for now. These will
remain missing in the merged dataset.

### Assessing Column names

Some inconsistencies were found in the column names across both
datasets. For example, the IMF dataset doesn’t have indicate a variable
name of the country names while the World Bank dataset uses “Country
Name”. Additionally, many columns are labeled with years (e.g., “1960”,
“1961”, “1962”), indicating a wide format structure. The action item to
take for this is to standardize column names to lower case and
consistent naming conventions (e.g., country, year, debt_pct_gdp,
growth_pct_gdp). This will make it easier to merge and reference
variables across datasets later.

### Assessing variable type

Most variables are read in as character type because the original Excel
and CSV files contain both text and numbers (e.g., missing values,
regional labels, or country names). Year columns should be numeric, but
some may have been imported as character strings. The action item to
take for this is to convert all year values and measurement columns to
numeric data types after reshaping the data to long format. Ensure that
numeric conversion handles blanks or symbols properly by turning them
into NA.

### Assessing data completeness

The years in the IMF ranges from 1800-2015 while the years in the World
Bank growth data goes from 1960-2021. Also, not every country has data
for every year. The action item to take for this is to restrict both
datasets to the 1960–2015 period to maximize overlap between them. After
filtering, visually verify completeness by counting the number of
non-missing values per year for both debt and growth datasets. Missing
values will be recorded as NA so that the merged dataset reflects true
data availability.

## Step 4: Clean and pre-process the data

2)  

``` r
# --- Debt ---
clean_debt_data <- function(df) {
  df %>%
    rename(country = 1) %>%                     # first col is country
    select(country, matches("^\\d{4}$")) %>%    # keep only year columns
    pivot_longer(
      -country,
      names_to   = "year",
      values_to  = "debt_pct_gdp",
      names_transform = list(year = as.integer)
    ) %>%
    mutate(
      # handle stray non-numeric tokens like ".."
      debt_pct_gdp = readr::parse_number(as.character(debt_pct_gdp))
    ) %>%
    filter(year >= 1960) %>%
    filter(!country %in% c("World","Euro area","High income","Low income","OECD members"))
}

# --- Growth ---
clean_growth_data <- function(df) {
  df %>%
    rename(country = `Country Name`) %>%
    select(country, matches("^\\d{4}$")) %>%
    pivot_longer(
      -country,
      names_to   = "year",
      values_to  = "growth_pct_gdp",
      names_transform = list(year = as.integer)
    ) %>%
    mutate(growth_pct_gdp = readr::parse_number(as.character(growth_pct_gdp))) %>%
    filter(year >= 1960) %>%
    filter(!country %in% c("World","Euro area","High income","Low income","OECD members"))
}

# Apply
debt_clean   <- clean_debt_data(debt_raw)
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `debt_pct_gdp =
    ##   readr::parse_number(as.character(debt_pct_gdp))`.
    ## Caused by warning:
    ## ! 31836 parsing failures.
    ## row col expected  actual
    ## 217  -- a number no data
    ## 218  -- a number no data
    ## 219  -- a number no data
    ## 220  -- a number no data
    ## 221  -- a number no data
    ## ... ... ........ .......
    ## See problems(...) for more details.

``` r
growth_clean <- clean_growth_data(growth_raw)

# --- Check Australia 1960–1969 example ---
debt_clean %>% filter(country == "Australia", year >= 1960, year <= 1969)
```

    ## # A tibble: 10 × 3
    ##    country    year debt_pct_gdp
    ##    <chr>     <int>        <dbl>
    ##  1 Australia  1960         31.5
    ##  2 Australia  1961         30.3
    ##  3 Australia  1962         30.4
    ##  4 Australia  1963         29.3
    ##  5 Australia  1964         27.6
    ##  6 Australia  1965         NA  
    ##  7 Australia  1966         41.2
    ##  8 Australia  1967         39.2
    ##  9 Australia  1968         38.2
    ## 10 Australia  1969         35.7

``` r
growth_clean %>% filter(country == "Australia", year >= 1960, year <= 1969)
```

    ## # A tibble: 10 × 3
    ##    country    year growth_pct_gdp
    ##    <chr>     <int>          <dbl>
    ##  1 Australia  1960          NA   
    ##  2 Australia  1961           2.48
    ##  3 Australia  1962           1.29
    ##  4 Australia  1963           6.21
    ##  5 Australia  1964           6.98
    ##  6 Australia  1965           5.98
    ##  7 Australia  1966           2.38
    ##  8 Australia  1967           6.30
    ##  9 Australia  1968           5.10
    ## 10 Australia  1969           7.04

3)  

``` r
## Step 4c — Pre-processing join (harmonize names + merge) ----

# 1) A small crosswalk for common WB vs IMF country name differences.
#    Left side = *IMF spelling*, Right side = *World Bank standard*.
imf_to_wb <- c(
  "United States of America" = "United States",
  "Korea, South"             = "Korea, Rep.",
  "Republic of Korea"        = "Korea, Rep.",
  "Korea (Republic of)"      = "Korea, Rep.",
  "Iran"                     = "Iran, Islamic Rep.",
  "Iran (Islamic Republic of)" = "Iran, Islamic Rep.",
  "Egypt"                    = "Egypt, Arab Rep.",
  "Gambia"                   = "Gambia, The",
  "Congo, Democratic Republic of" = "Congo, Dem. Rep.",
  "Democratic Republic of the Congo" = "Congo, Dem. Rep.",
  "Congo, Republic of"       = "Congo, Rep.",
  "Russian Federation"       = "Russian Federation", # (WB already uses this)
  "Russia"                   = "Russian Federation",
  "Venezuela"                = "Venezuela, RB",
  "Lao People's Democratic Republic" = "Lao PDR",
  "Yemen"                    = "Yemen, Rep.",
  "Bahamas"                  = "Bahamas, The",
  "Hong Kong"                = "Hong Kong SAR, China",
  "Macao"                    = "Macao SAR, China",
  "Palestine, State of"      = "West Bank and Gaza",
  "Cabo Verde"               = "Cabo Verde"          # (example keeping as-is)
)

# 2) Build a standardized key in each table (target standard = World Bank names).
normalize_name <- function(x) {
  x %>%
    str_squish() %>%
    str_replace_all("\\s+", " ")                # collapse weird spacing
}

growth_std <- growth_clean %>%
  mutate(country_std = normalize_name(country)) %>%
  select(country_std, country, year, growth_pct_gdp)

debt_std <- debt_clean %>%
  mutate(country = normalize_name(country)) %>%
  mutate(country_std = recode(country, !!!imf_to_wb, .default = country)) %>%
  select(country_std, country, year, debt_pct_gdp)

# 3) Quick diagnostics: which names still don’t match?
#    (Countries present in one table but not the other after standardization)
unmatched_in_growth <- anti_join(growth_std %>% distinct(country_std),
                                 debt_std   %>% distinct(country_std),
                                 by = "country_std")
unmatched_in_debt   <- anti_join(debt_std   %>% distinct(country_std),
                                 growth_std %>% distinct(country_std),
                                 by = "country_std")


# If you see a few lingering mismatches, add them to `imf_to_wb` above and rerun.

# 4) Merge on standardized country + year (keep original names for readability)
combined_data <- full_join(
  debt_std  %>% select(country_std, year, debt_pct_gdp),
  growth_std %>% select(country_std, year, growth_pct_gdp),
  by = c("country_std", "year")
) %>%
  # prefer the WB country label when available, fallback to IMF one
  left_join(growth_std %>% select(country_std, country) %>% distinct(),
            by = "country_std") %>%
  rename(country_wb = country) %>%
  left_join(debt_std %>% select(country_std, country) %>% distinct(),
            by = "country_std") %>%
  rename(country_imf = country) %>%
  mutate(country = coalesce(country_wb, country_imf, country_std)) %>%
  select(country, year, debt_pct_gdp, growth_pct_gdp) %>%
  arrange(country, year)

# 5) Sanity check: Australia 1960–1969 should match the example table
combined_data %>%
  filter(country == "Australia", dplyr::between(year, 1960, 1969))
```

    ## # A tibble: 10 × 4
    ##    country    year debt_pct_gdp growth_pct_gdp
    ##    <chr>     <int>        <dbl>          <dbl>
    ##  1 Australia  1960         31.5          NA   
    ##  2 Australia  1961         30.3           2.48
    ##  3 Australia  1962         30.4           1.29
    ##  4 Australia  1963         29.3           6.21
    ##  5 Australia  1964         27.6           6.98
    ##  6 Australia  1965         NA             5.98
    ##  7 Australia  1966         41.2           2.38
    ##  8 Australia  1967         39.2           6.30
    ##  9 Australia  1968         38.2           5.10
    ## 10 Australia  1969         35.7           7.04
