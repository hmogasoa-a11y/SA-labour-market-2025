# SA-labour-market-2025
Analyzed Statistics South Africa QLFS microdata to identify year-on-year employment shifts across 10 industries. Identified +73k Transport and +60k Construction job gains offset by -130k Manufacturing and -122k Trade losses, resulting in net -55k jobs YoY.

## Overview
This project analyzes employment shifts in South Africa using Statistics South Africa's Quarterly Labour Force Survey (QLFS) microdata. It compares Q4 2024 vs Q4 2025 across 10 industries to identify sectoral trends.

**Key methodological insight**: Stats SA implemented revised population benchmarks in Q4 2025 based on the 2022 Census. This creates a break in the series, making quarter-on-quarter comparisons for 2025 misleading. This analysis uses year-on-year methodology to ensure valid comparisons.

## Key Findings
- **Total employment**: Declined by 54,959 jobs YoY to 17.3M in Q4 2025 -0.3%
- **Growth sectors**: Transport +73,423 jobs +6.8%, Construction +60,208 +4.4%
- **Declining sectors**: Manufacturing -129,571 jobs -7.6%, Trade -122,443 -3.5%
- **Net effect**: Job losses concentrated in 2 sectors, while 5 of 10 industries posted gains

Full 10-industry breakdown and methodology available in the report PDF.

## Repository Structure

[South African Market Q4 2025_ Year-on-Year Industry Shifts and Why Quarter-to-Quarter is Misleading.pdf](https://github.com/user-attachments/files/28309887/South.African.Market.Q4.2025_.Year-on-Year.Industry.Shifts.and.Why.Quarter-to-Quarter.is.Misleading.pdf)


## Data Source
**Primary data**: Statistics South Africa. Quarterly Labour Force Survey 2024: Quarter 4, Version 1. Statistics South Africa. Quarterly Labour Force Survey 2025: Quarter 4, Version 1. Pretoria: Stats SA [producer], 2024-2025.

**Access**: Microdata obtained from DataFirst, University of Cape Town. https://www.datafirst.uct.ac.za/

**Note**: Due to DataFirst licensing restrictions, raw microdata files are not included in this repository.

## Tools Used
R · tidyverse · data.table · QLFS microdata · DataFirst

## Author
Gomolemo Bokang Mogasoa

## License
Code: MIT License
Report: CC BY-SA 4.0

---
*This is an independent analysis. Statistics South Africa and DataFirst are not responsible for the analysis or interpretations presented here.*
