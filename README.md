
SA Labour Market: Q4 2024 vs Q4 2025 Industry Analysis

Overview
This repo calculates real year-on-year employment changes by industry in South Africa, comparing Q4 2024 to Q4 2025. The analysis accounts for Stats SA’s post-Census 2022 weight revision implemented in Q2 2025, which creates a structural break in the QLFS series.

Key Finding
Quarter-on-quarter comparisons within 2025 are not valid due to the new weights. This project uses Q4-to-Q4 year-on-year growth to strip out the reweighting effect and measure real employment change.
Data SourcesStats SA Quarterly Labour Force Survey: Q4 2024 Microdata, Q4 2025 MicrodataVariables used: Indus, Lfs_Status/Status, WeightSource: Statistics South Africa, SuperCROSS database

Methodology
1. Harmonization: Stats SA changed industry coding between quarters. Q4 2024 uses full text labels while Q4 2025 uses numeric SIC codes 1-10. Mapped using official QLFS metadata.
2. Filtering: Employed persons only. Status == "Employed" for 2024, Lfs_Status == 1 for 2025.
3. Weighting: Applied person weights Weight to get population-level estimates. NA values removed
4. Aggregation: Summed employment by 10 main industries for each quarter.
5. YoY Calculation: YoY_abs = Q4_2025 - Q4_2024. YoY_pct = YoY_abs / Q4_2024 * 100

Files

FileDescription sa_labour_yoy.R Main analysis script. Commented for reproducibility / q4_2024_vs_q4_2025_industry_yoy.csv Final output table with totals and industry shares / data /Place QLFS microdata files here. Not included due to size

How to Run

1. Download QLFS Q4 2024 and Q4 2025 microdata from Stats SA
2. Place files in data/ and update filenames in sa_labour_yoy.R if needed
3. Run sa_labour_yoy.R in R/RStudio
4. Output CSV exports automatically

Packages
dplyr, readr

Key Assumptions & Caveats

1. Series break: Q2 2025 weights based on Census 2022 create non-comparability with Q1 2025 and earlier quarters. YoY Q4-on-Q4 is the earliest valid comparison post-revision.
2. Industry mapping: Manual mapping between 2024 text and 2025 numeric codes based on Stats SA documentation. See indus_map in script.
3. Excluded categories: Indus == 11 "Other/Unspecified" and missing values dropped for consistency.
4. Rounding: Employment figures rounded to nearest whole number in final output.

Results Preview
The script outputs a table with Q4 2024 employment, Q4 2025 employment, absolute change, YoY %, and industry shares. Full results in q4_2024_vs_q4_2025_industry_yoy.csv.

Why This Matters
Most public commentary on 2025 employment uses QoQ numbers that are distorted by the weight update. This YoY approach shows real labour market dynamics in key sectors like Construction, Trade, and Community Services.

Author
Gomolemo Bokang Mogasoa
https://www.linkedin.com/in/gomolemo-mogasoa/
May 2026

sa_labour_yoy.R
# SA Labour: Q4 2024 vs Q4 2025 Industry YoY
# Author: Gomolemo Bokang Mogasoa
# Purpose: Calculate real YoY employment change by industry, stripping out Q2 2025 weight revision effect
# Note: QoQ 2025 comparisons invalid due to Stats SA post-Census 2022 weight update. Use YoY only.

# ---- 1. SETUP ----
library(readr)
library(dplyr)

# ---- 2. LOAD DATA ----
# Q4 2024: industry names stored as text, employment status in Status column
q4_2024 <- read_csv("qlfs-2024-q4-worker-v1.csv")

# Q4 2025: industry stored as numeric codes, employment status in Lfs_Status column  
q4_2025_raw <- read_csv("qlfs-2025-q4-v1.csv")

# ---- 3. HARMONIZE INDUSTRY CODES ----
# Stats SA changed industry variable between quarters: text in 2024, numeric in 2025
# Mapping based on QLFS metadata + SIC codes
indus_map <- data.frame(
  Indus_text = c("Agriculture; hunting; forestry and fishing",
                 "Mining and quarrying",
                 "Manufacturing", 
                 "Electricity; gas and water supply",
                 "Construction",
                 "Wholesale and retail trade",
                 "Transport; storage and communication",
                 "Financial intermediation; insurance; real estate and business services",
                 "Community; social and personal services",
                 "Private households"),
  Indus_code = 1:10,
  Industry = c("Agriculture", "Mining", "Manufacturing", "Utilities",
               "Construction", "Trade", "Transport", "Finance",
               "Community & personal services", "Private households")
)

# ---- 4. CALCULATE Q4 2024 WEIGHTED TOTALS ----
q4_2024_all <- q4_2024 %>%
  filter(Status == "Employed", Indus %in% indus_map$Indus_text) %>% # drop invalid/missing
  left_join(indus_map, by = c("Indus" = "Indus_text")) %>%
  group_by(Indus_code, Industry) %>%
  summarise(q4_2024_emp = sum(Weight, na.rm = TRUE), .groups = "drop")

# ---- 5. CALCULATE Q4 2025 WEIGHTED TOTALS ----
# Lfs_Status == 1 = Employed in 2025 file. Drop Indus == 11 "Other/Unspecified"
q4_2025_all <- q4_2025_raw %>%
  filter(Lfs_Status == 1, Indus %in% 1:10) %>%
  group_by(Indus) %>%
  summarise(q4_2025_emp = sum(Weight, na.rm = TRUE), .groups = "drop") %>%
  rename(Indus_code = Indus)

# ---- 6. MERGE + CALCULATE YOY ----
full_yoy <- indus_map %>%
  select(Indus_code, Industry) %>%
  left_join(q4_2024_all %>% select(-Industry), by = "Indus_code") %>%
  left_join(q4_2025_all, by = "Indus_code") %>%
  mutate(
    yoy_abs = q4_2025_emp - q4_2024_emp,
    yoy_pct = round(yoy_abs / q4_2024_emp * 100, 1)
  ) %>%
  select(Industry, q4_2024_emp, q4_2025_emp, yoy_abs, yoy_pct) %>%
  arrange(desc(yoy_abs))

# ---- 7. ADD TOTALS + SHARES FOR FINAL TABLE ----
full_yoy_clean <- full_yoy %>%
  mutate(across(c(q4_2024_emp, q4_2025_emp, yoy_abs), ~round(.x, 0)))

full_yoy_totals <- full_yoy_clean %>%
  bind_rows(
    summarise(.,
              Industry = "Total",
              q4_2024_emp = sum(q4_2024_emp, na.rm = TRUE),
              q4_2025_emp = sum(q4_2025_emp, na.rm = TRUE),
              yoy_abs = sum(yoy_abs, na.rm = TRUE)
    ) %>%
      mutate(yoy_pct = round(yoy_abs / q4_2024_emp * 100, 1))
  ) %>%
  mutate(
    share_2024 = round(q4_2024_emp / q4_2024_emp[Industry == "Total"] * 100, 1),
    share_2025 = round(q4_2025_emp / q4_2025_emp[Industry == "Total"] * 100, 1)
  )

# ---- 8. EXPORT ----
print(full_yoy_totals, row.names = FALSE)
write.csv(full_yoy_totals, "q4_2024_vs_q4_2025_industry_yoy.csv", row.names = FALSE)

# ---- 9. METHOD NOTE ----
# Weight revision: Stats SA implemented new Census 2022 weights in Q2 2025.
# This creates a series break. Q1 2025 to Q4 2025 not directly comparable.
# YoY Q4 2025 vs Q4 2024 uses consistent methodology and is valid.
