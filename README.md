# GSA Inventory of Owned and Leased Properties (IOLP): Data Analysis

An exploratory data analysis of the U.S. General Services Administration's federal real estate portfolio, covering properties across all 50 states, Washington D.C., Puerto Rico, Guam, and American Samoa.



## Project Overview

The GSA manages one of the largest and most geographically distributed real estate portfolios in the United States. This project analyzes the IOLP dataset to understand the composition of that portfolio, identify where lease expiration pressure is concentrated, and surface the specific properties that represent the highest renewal risk.

For this analysis, I built a **Lease Risk Scoring framework** that ranks every leased property by a weighted combination of expiration urgency, property size, and utilization pressure. The goal is to answer a practical question: *when dozens of leases expire in the same year, which ones actually need attention first?*



## Dataset

**Source:** [data.gov: Inventory of Owned and Leased Properties (IOLP)](https://catalog.data.gov/dataset/inventory-of-owned-and-leased-properties-iolp)

The dataset is published by the GSA and updated periodically. This analysis uses the **February 2026** release.

| File | Rows | Description |
|---|---|---|
| `iolp-buildings.xlsx` | 8,504 | Owned and leased building records |
| `iolp-leases.xlsx` | 7,381 | Individual lease records with dates |

To reproduce this analysis, download both files from the link above and place them in `data/raw/`.



## Key Findings

### Portfolio Scale
- **8,504 total properties** spanning 50 states and territories
- **359,406,841 sq ft** of total rentable space, averaging 42,263 sq ft per property
- **79.4% leased** (6,748 properties / 173.5M sq ft) vs. 20.6% federally owned

### Lease Expiration Pressure
| Window | Properties Expiring |
|---|---|
| Within 1 year | 781 |
| Within 2 years | 1,396 |
| Within 5 years | 2,955 |

The single largest expiration year is **2026**, with 705 properties and ~13M sq ft at risk, meaning a significant share of those leases were already in urgent territory at the time of this analysis.

### Geographic Concentration
Top 5 states by **property count**: TX (901), CA (706), FL (385), NY (352), VA (335)

Top 5 states by **total square footage**: DC (50.4M), MD (28.4M), CA (27.9M), VA (23.9M), TX (23.6M)

Washington D.C. stands out: despite ranking 7th by property count, it holds the largest footprint by far and has the highest federal ownership rate (63.6%) of any jurisdiction, consistent with its role as the seat of government.

### Lease Risk Scoring
Risk score (0–100) for every leased property using three weighted components:

| Component | Weight | Logic |
|---|---|---|
| Expiration Proximity | 60% | Leases expiring sooner score higher |
| Size Exposure | 30% | Larger properties score higher (percentile-ranked) |
| Utilization Pressure | 10% | Fully occupied buildings score higher |

Risk tier distribution across 6,257 scored leased properties:
- **High** (score > 80): 415 properties
- **Medium** (score 50-80): 1,583 properties
- **Low** (score < 50): 4,259 properties




## Project Structure

```
IOLP-GSA-Analysis/
├── README.md
├── requirements.txt
├── .gitignore
├── IOLP_data_analysis.ipynb       ← Main analysis notebook
├── data/
│   └── raw/                       ← Place downloaded IOLP files here
└── data/processed/                ← Auto-generated on notebook run
    ├── buildings_cleaned.xlsx
    ├── leases_cleaned.xlsx
    ├── master_dataset.xlsx
    ├── lease_cliff_analysis.xlsx
    ├── state_summary.xlsx
    └── high_risk_leases.xlsx      ← Risk-scored priority list
```

---

## How to Run

**1. Clone the repo**
```bash
git clone https://github.com/antamkha-nguyen/IOLP-GSA-Analysis.git
cd IOLP-GSA-Analysis
```

**2. Install dependencies**
```bash
pip install -r requirements.txt
```

**3. Download the data**

Go to [catalog.data.gov](https://catalog.data.gov/dataset/inventory-of-owned-and-leased-properties-iolp), download the two IOLP Excel files, and place them in `data/raw/`. Rename the files to match the placeholders below, or set the `BUILDINGS_FILE` and `LEASES_FILE` environment variables to your actual filenames:
- `iolp-buildings.xlsx`
- `iolp-leases.xlsx`


**4. Run the notebook**
```bash
jupyter notebook IOLP_data_analysis.ipynb
```

Run all cells top to bottom. Processed outputs will be saved automatically to `data/processed/`.



## Notebook Structure

| Section | Description |
|---|---|
| 1. Environment Setup | Library imports and configurable file path / date parameters |
| 2. Data Loading | Load both raw Excel files and inspect dimensions |
| 3. Data Cleaning | Numeric coercion, date parsing, state normalization, feature engineering |
| 4. Master Dataset | Merge buildings and leases on Location Code; resolve duplicate keys |
| 5. Risk Scoring | Build composite Lease_Risk_Score and assign Low/Medium/High tiers |
| 6. Save Cleaned Data | Export cleaned datasets to processed folder |
| 7. Portfolio Overview | Summary statistics: count, sq ft, ownership breakdown, regional distribution |
| 8. Lease Cliff Analysis | Year-by-year expiration volume; High-risk tier priority list |
| 9. Visualizations | Top states, state sq ft pie, geographic property map, regional portfolio mix |
| 10. Risk Analysis | Risk tier by region, risk landscape scatter plot, top 20 priority list |
| 11. Export | Save all analytical outputs to processed folder |



## Tools & Libraries

- **Python 3.11**
- `pandas`: data manipulation
- `numpy`: numerical operations
- `plotly`: interactive visualizations
- `matplotlib` / `seaborn`: static charts
- `openpyxl`: Excel I/O



## Data Limitations

- `Available Square Feet` is **100% null** in both datasets. Utilization analysis is not possible with this release.
- `Construction Date` is missing for **~80% of buildings**. Age analysis is limited to the available subset.
- `Installation Name` is missing for ~86% of records. Treated as non-essential.
- The lease merge uses the **latest expiration date** for locations with multiple lease records (696 locations affected).



## Author

**An Tam Kha (Justin) Nguyen**
 Updated (Feb 2026)
