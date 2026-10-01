# Replication package: PSO-VWCM ensemble and Monte Carlo forecasting of Pakistan's electricity consumption

Code and data for: Nazir, M. U., & Li, J. *A hybrid PSO-ensemble and Monte Carlo approach for electricity consumption forecasting in Pakistan, with heat-sensitivity analysis* (manuscript NXSUST-D-26-01053, revised version).

Running the scripts below in order reproduces every table and figure in the manuscript from the input data.

## 1. Requirements

Python 3.12 and the packages in `requirements.txt`:

```
pip install -r requirements.txt
```

All random processes use fixed seeds (neural networks and PSO: seeds 0–4; Monte Carlo: 20260922), so results are reproducible.

## 2. Input data

| File | Content | Source |
|---|---|---|
| `sector_data_harmonised.csv` | Annual electricity consumption (GWh) by sector, FY1972–FY2024, with population | Built from the two Economic Survey tables below (see `data_provenance.csv`) |
| `pes2009_10_table12_1_electricity.csv` | Economic Survey 2009-10, Statistical Supplement, Table 12.1, electricity (FY1972–FY2010) | Finance Division (2010) |
| `pes_supplement_2024_25_table14_1_electricity.csv` | Economic Survey 2024-25, Statistical Supplement, Table 14.1, electricity (FY2001–FY2025) | Finance Division (2025), p. 211 |
| `daily_timeseries_1974_2024.csv` | ERA5 daily 2 m temperature and related fields, area-averaged over Pakistan, 1974–2024 | Copernicus Climate Change Service (Hersbach et al., 2020) |
| `pakistan_electricity_plus_extremes_1974_2024.csv` | Annual heat indices computed from the ERA5 daily series (TXx, T95, CDD, heatwave days). The column `ELEC_GWh` is from an earlier compilation and is **not used**; electricity data come from `sector_data_harmonised.csv`. | Derived from ERA5 |
| `igcep_2025_35_country_forecast.csv` | IGCEP 2025–35 country energy and peak forecasts, three scenarios (Tables 4-8 to 4-10, pp. 35–36) | ISMO (2025) |
| `data_provenance.csv` | Source table and page for every series, checks performed, and treatment of anomalies (FY1991 street lighting; FY2022 other government) | — |

## 3. Run order

| Step | Command | Main outputs | Manuscript items |
|---|---|---|---|
| 1 | `python stage1_rolling.py` (about 5 minutes) | `rolling_<sector>.csv`, `cfg_<sector>.json` | Rolling-origin forecasts, Section 3.3 |
| 2 | `python combine.py` | `combined_forecasts.csv`, `weights.json`, `metrics.txt` | PSO-VWCM weights and accuracy, Section 3.4 |
| 3 | `python final_forecast.py` | `final_forecasts_2023_2037.csv` (covers 2025–2037), `final_model_config.json` | 2025–2037 forecasts; Table 4 |
| 4 | `python stage2_regression.py` | `history.csv`, `table9_robustness.csv` | Tables 12 and 13 |
| 5 | `python stage2_final_mc.py` | `table11_revised_combined.csv`, `sim_<scenario>.npz` | Table 14 |
| 6 | `python calibration.py` | `calibration.csv` | Figure 15 |
| 7 | `python make_tables.py` | `tables_3_8_revised.md` | Tables 5–11 |
| 8 | `python make_figures.py` | `figures/` (data figures) | Figures 1, 9–17, S1–S2 |
| 9 | `python make_framework_figure.py`; `python make_bpnn_figure.py`; `python make_lstm_figures.py`; `python make_method_figures.py` | `figures/` (method diagrams) | Figures 2–8 |

The folder `results/` contains the outputs of steps 1–7 as produced for the manuscript, for comparison.

## 4. Correspondence between output files and manuscript numbering

Some output files keep the working names used during the analysis:

| Output file | Manuscript |
|---|---|
| `tables_3_8_revised.md` (sector blocks and national block) | Tables 5–10 (sectors) and Table 11 (national) |
| `table9_robustness.csv` | Tables 12 (row 1) and 13 (all rows) |
| `table11_revised_combined.csv` | Table 14 |
| `table2_revised.csv` | Table 2 |
| `figures/Fig01_*` | Figure 1 |
| `figures/Fig02_framework.*` | Figure 2 |
| `figures/Fig05_BPNN.*` | Figure 3 |
| `figures/Fig02_LSTM_gates.*`, `Fig03_LSTM_memory_update.*`, `Fig04_LSTM_cell.*` | Figures 4, 5, 6 |
| `figures/Fig06_PSO_flowchart.*`, `Fig07_weight_updating.*` | Figures 7, 8 |
| `figures/Fig09_*`–`Fig26_*` (three panels per sector) | Figures 9–14 (one combined figure per sector: residential, agricultural, commercial, industrial, street lighting, other government) |
| `figures/Fig33_interval_calibration.*` | Figure 15 |
| `figures/Fig31_2037_scenario_distributions.*` | Figure 16 |
| `figures/Fig34_FY2025_holdout.*` | Figure 17 |
| `figures/FigS1_*`, `FigS2_*` | Supplementary Figures S1–S2 |

## 5. Method summary

Six standalone models (ARIMA, GM(1,1), SVR, BPNN, Holt's exponential smoothing, LSTM) are estimated for each of six consumer sectors on an expanding-window rolling origin (origins 1996–2023, horizons 1–5 years). Combination weights are estimated by particle swarm optimization on the probability simplex using only past out-of-sample errors, and updated across origins (Eqs. 12–13). Stage 2 estimates the association between annual changes in TXx and in national consumption (OLS, Newey–West HAC) and simulates 20,000 Monte Carlo paths per scenario around the Stage-1 national forecast, including coefficient, temperature, residual and Stage-1 forecast uncertainty (Eqs. 14–17). See Sections 3.2–3.6 of the manuscript.

## 6. Licence

Code: MIT License. Processed data: CC BY 4.0. ERA5 data are provided under the Copernicus licence and must be attributed to the Copernicus Climate Change Service. Economic Survey and IGCEP data are public government publications; cite the original sources listed above.

## 7. Citation

Please cite the article and this repository: [[Zenodo DOI, added after upload].](https://github.com/DRUMAIRNAZIR/A-Hybrid-PSO-Ensemble-and-Monte-Carlo) 
