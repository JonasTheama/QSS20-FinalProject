# Offshore Finance: Geographic Pipelines, Broker Concentration, and the Causal Effect of the Panama Papers

**Jonas Thieme | QSS 20: Modern Statistical Computing | Dartmouth College | Spring 2026**

This project uses the [ICIJ Offshore Leaks Database](https://offshoreleaks.icij.org/pages/database), combining the Panama Papers, Pandora Papers, Paradise Papers, Offshore Leaks, and Bahamas Leaks, to characterize structural features of the global offshore financial system and estimate the causal effect of the 2016 Panama Papers release on incorporation behavior.

---

## Key Findings

- **31.8%** of all offshore entities are registered in the British Virgin Islands
- The Panama Papers release caused an immediate drop of **1,004 incorporations/month** (p < 0.001), with a simultaneous rise in Bahamas incorporations consistent with a substitution effect
- A logistic regression predicting entity default achieves **AUC = 0.906**; Panama Papers source is the strongest single predictor of default
- A small number of intermediary brokers account for a disproportionate share of all broker–entity connections

---

## Repository Structure

```
QSS20-FinalProject/
├── README.md
├── code/
│   ├── 00_load.ipynb          ← data loading and path setup
│   ├── 01_explore.ipynb       ← missingness, distributions, leak source filter
│   ├── 02_analyze.ipynb       ← flow heatmap and arc map
│   ├── 03_logistic_regression.ipynb   ← predicting entity default status
│   ├── 04_interrupted_time_series.ipynb  ← Panama Papers causal analysis
│   └── 05_intermediary_analysis.ipynb    ← broker degree centrality and concentration
├── output/
│   ├── viz3_flow_heatmap_clean.png
│   ├── viz4_flow_map.html
│   ├── viz5_roc_curve.png
│   ├── viz6_coef_plot.png
│   ├── viz7_its_overall.png
│   ├── viz8_substitution_effect.png
│   ├── viz9_intermediary_concentration.png
│   ├── viz10_top_intermediaries.png
│   ├── viz11_intermediary_countries.png
│   ├── table1_logistic_regression_summary.csv
│   ├── table2_logistic_regression_coefficients.csv
│   ├── table3_its_results.csv
│   ├── table4_intermediary_concentration.csv
│   └── table5_top_intermediaries.csv
└── data/
    └── README_data.md         ← instructions for accessing the raw data
```

---

## Notebook Guide

| Notebook | Inputs | What it does | Outputs |
|----------|--------|--------------|---------|
| `00_load.ipynb` | Raw CSVs from ICIJ | Sets `BASE_PATH`, loads all six CSVs, prints shapes and column names | None (run first) |
| `01_explore.ipynb` | Raw CSVs | Missingness analysis, relationship type distributions, leak source filter, starter bar charts | `viz1_officer_countries.png`, `viz2_entity_jurisdictions.png` |
| `02_analyze.ipynb` | Filtered entities + officers + relationships | Officer→entity flow matrix, heatmap, arc map | `viz3_flow_heatmap_clean.png`, `viz4_flow_map.html` |
| `03_logistic_regression.ipynb` | Filtered entities + officers + relationships | Feature engineering, logistic regression, ROC curve, coefficient plot, summary tables | `viz5_roc_curve.png`, `viz6_coef_plot.png`, `table1_*.csv`, `table2_*.csv` |
| `04_interrupted_time_series.ipynb` | Filtered entities | Monthly incorporation counts, ITS regression, substitution effect robustness check | `viz7_its_overall.png`, `viz8_substitution_effect.png`, `table3_*.csv` |
| `05_intermediary_analysis.ipynb` | Relationships + intermediaries + entities | Degree centrality, concentration curve, geographic distribution | `viz9_*.png`, `viz10_*.png`, `viz11_*.png`, `table4_*.csv`, `table5_*.csv` |

---

## Data

The raw data files are not included in this repository — they are too large for GitHub (~1GB total). Download the full ICIJ Offshore Leaks Database from:

> [https://offshoreleaks.icij.org/pages/database](https://offshoreleaks.icij.org/pages/database)

The six required files are:

| File | Rows | Description |
|------|------|-------------|
| `relationships.csv` | 3,339,267 | All edges in the network |
| `nodes-entities.csv` | 814,344 | Shell companies and offshore entities |
| `nodes-officers.csv` | 771,315 | Officers, directors, shareholders |
| `nodes-intermediaries.csv` | 25,629 | Law firms and broker intermediaries |
| `nodes-addresses.csv` | 402,246 | Address nodes |
| `nodes-others.csv` | 2,989 | Miscellaneous node types |

### Setting your data path

Each notebook has a configuration cell at the top. **Either** set an environment variable before running:

```bash
export ICIJ_DATA_PATH="/path/to/your/full-oldb.LATEST/"
```

**Or** edit the fallback path directly in the first cell of any notebook:

```python
import os
DATA_PATH = os.environ.get(
    'ICIJ_DATA_PATH',
    '/your/local/path/to/full-oldb.LATEST/'   # <- change this
)
BASE_PATH = DATA_PATH if DATA_PATH.endswith('/') else DATA_PATH + '/'
```

The notebooks do not depend on any files being present in the `data/` folder of this repository.

---

## Reproducing the Analysis

Run notebooks in order: `00` → `01` → `02` → `03` → `04` → `05`. Each notebook is self-contained after the data path is set. Dependencies:

```bash
pip install pandas numpy matplotlib seaborn plotly scikit-learn statsmodels
```

---

## Important Data Notes

1. **Leak source filter:** All analysis restricts to leak sources only, excluding corporate registry dumps (Malta, Barbados, Aruba, Samoa, Nevis, Cook Islands, Bahamas, Lebanon). See `01_explore.ipynb` for the full `LEAK_SOURCES` list and rationale.
2. **Officers ≠ beneficial owners:** Most officers in the dataset are nominee directors or professionals, not the underlying wealthy individuals.
3. **No monetary values:** The dataset contains structural relationships only. Global offshore wealth is estimated externally at roughly 8% of global household financial assets (Zucman, 2013).
4. **Biased sample:** We observe only what was leaked. The true offshore system is substantially larger.
5. **Semicolon-delimited fields:** The `countries` field in officers and intermediaries can contain multiple semicolon-joined values. All analysis explodes these before aggregating.

---

## Citation

If you use this analysis, please also cite the underlying data:

> International Consortium of Investigative Journalists. *ICIJ Offshore Leaks Database*. 2023. https://offshoreleaks.icij.org/pages/database
