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
