Replication Package: A Hybrid PSO‑Ensemble and Monte‑Carlo Approach for Electricity Consumption Forecasting in Pakistan, with Heat‑Sensitivity Analysis

Code and complete reproducible files for Nazir, M. U., & Li, J. A Hybrid PSO‑Ensemble and Monte‑Carlo Approach for Electricity Consumption Forecasting in Pakistan, with Heat‑Sensitivity Analysis (Manuscript ID: NXSUST‑D‑26‑01053, revised version).

Execute the scripts sequentially as outlined below to fully reproduce all tables, figures and numerical results reported in the manuscript using the provided input datasets.

1. Requirements
This project uses Python 3.12. Install required dependencies from requirements.txt:

pip install -r requirements.txt

All stochastic computations use fixed random seeds to guarantee reproducibility:
‑ Neural‑network and PSO optimisations: seeds 0‑4
‑ Monte‑Carlo simulations: seed 20260922

2. Input Datasets
All input CSV files are included within this repository. Below is a summary of each dataset, its content and original source.

File	Description	Source
sector_data_harmonised.csv	Annual sector‑wise electricity consumption (GWh), FY1972‑FY2024, plus population metrics	Compiled from Pakistan Economic Survey source tables (see data_provenance.csv)
pes2009_10_table12_1_electricity.csv	Economic Survey 2009‑10 Statistical Supplement Table 12.1; electricity data FY1972‑FY2010	Finance Division (2010)
pes_supplement_2024_25_table14_1_electricity.csv	Economic Survey 2024‑25 Statistical Supplement Table 14.1; electricity data FY2001‑FY2025	Finance Division (2025), p. 211
daily_timeseries_1974_2024.csv	ERA5 daily 2‑metre temperature and auxiliary meteorological variables, area‑averaged for Pakistan, 1974‑2024	Copernicus Climate Change Service (Hersbach et al., 2020)
pakistan_electricity_plus_extremes_1974_2024.csv	Annual heat‑related indices derived from ERA5 daily observations (TXx, T95, CDD, heat‑wave days). Note: the ELEC_GWh column in this file is unused; electricity consumption is sourced exclusively from sector_data_harmonised.csv.	Derived from ERA5 reanalysis
igcep_2025_35_country_forecast.csv	IGCEP 2025‑2035 national‑level energy and peak‑load projections across three scenarios (Tables 4‑8 to 4‑10, pp. 35‑36)	ISMO (2025)
data_provenance.csv	Full provenance log for every time‑series variable, quality‑control checks and anomaly‑handling notes (including FY1991 street‑lighting and FY2022 other‑government adjustments)	—

3. Execution Workflow
Run the scripts in the listed order. Approximate runtime for stage1_rolling.py is ~5 minutes. The results/ folder contains pre‑computed outputs from the manuscript for cross‑comparison.

Step	Command	Primary Output Files	Corresponding Manuscript Sections & Items
1	python stage1_rolling.py	rolling_<sector>.csv, cfg_<sector>.json	Rolling‑origin forecasting workflow (Section 3.3)
2	python combine.py	combined_forecasts.csv, weights.json, metrics.txt	PSO‑VWCM ensemble weight optimisation and forecasting performance metrics (Section 3.4)
3	python final_forecast.py	final_forecasts_2023_2037.csv (2025‑2037 projections), final_model_config.json	2025‑2037 long‑term forecasts; Table 4
4	python stage2_regression.py	history.csv, table9_robustness.csv	Robust regression outputs; Tables 12, 13
5	python stage2_final_mc.py	table11_revised_combined.csv, sim_<scenario>.npz	Monte‑Carlo probabilistic simulation outputs; Table 14
6	python calibration.py	calibration.csv	Uncertainty‑interval calibration results; Figure 15
7	python make_tables.py	tables_3_8_revised.md	Main manuscript result tables; Tables 5‑11
8	python make_figures.py	Output figures saved under figures/	Figures 1, 9‑17, Supplementary S1‑S2
9	python make_framework_figure.py
python make_bpnn_figure.py
python make_lstm_figures.py
python make_method_figures.py	Additional method‑diagram figures saved under figures/	Figures 2‑8

Pre‑computed benchmark outputs from Steps 1‑7 are stored in the results/ directory for validation against locally‑run results.

4. Output‑File to Manuscript Mapping
Script output filenames reflect internal working names used during analysis. This table maps output files to final manuscript table/figure numbering.

Output File	Manuscript Item
tables_3_8_revised.md (sector‑level and national blocks)	Tables 5‑10 (sector‑wise), Table 11 (national aggregate)
table9_robustness.csv	Table 12 (Row 1), Table 13 (all rows)
table11_revised_combined.csv	Table 14
table2_revised.csv	Table 2
figures/Fig01_*	Figure 1
figures/Fig02_framework.*	Figure 2
figures/Fig05_BPNN.*	Figure 3
figures/Fig02_LSTM_gates.*, figures/Fig03_LSTM_memory_update.*, figures/Fig04_LSTM_cell.*	Figures 4, 5, 6
figures/Fig06_PSO_flowchart.*, figures/Fig07_weight_updating.*	Figures 7, 8
figures/Fig09_* – figures/Fig26_* (three panels per sector)	Figures 9‑14 (residential, agricultural, commercial, industrial, street‑lighting, other‑government sectors)
figures/Fig33_interval_calibration.*	Figure 15
figures/Fig31_2037_scenario_distributions.*	Figure 16
figures/Fig34_FY2025_holdout.*	Figure 17
figures/FigS1_*, figures/FigS2_*	Supplementary Figures S1‑S2

5. Method Overview
Six base forecasting models (ARIMA, GM(1,1), SVR, BPNN, Holt’s exponential smoothing, LSTM) are fitted for each of Pakistan’s six electricity‑consumption sectors within an expanding‑window rolling‑origin framework (origins 1996‑2023; forecast horizons 1‑5 years). Particle‑Swarm Optimisation (PSO) is applied over the probability simplex to derive time‑varying ensemble weights using only out‑of‑sample historical prediction errors (Equations 12‑13).

In Stage 2, ordinary least‑squares regression with Newey‑West HAC standard errors quantifies the empirical linkage between annual maximum temperature (TXx) and national‑level electricity consumption change. A total of 20 000 Monte‑Carlo realisations are generated for each scenario, propagating uncertainty from regression coefficients, temperature variability, model residuals and Stage‑1 forecast errors (Equations 14‑17). Refer to manuscript Sections 3.2‑3.6 for full methodological detail.

6. Licence Information
‑ Source code: MIT License
‑ Processed compiled datasets: CC BY 4.0
‑ ERA5 meteorological data: distributed under standard Copernicus licence; please include proper attribution to the Copernicus Climate Change Service in reuse.
‑ Pakistan Economic Survey and IGCEP datasets are public government publications; cite the respective original official sources listed above.

7. Citation
If you reuse code or datasets from this repository please cite the associated journal manuscript.

Nazir, M. U., & Li, J. A Hybrid PSO‑Ensemble and Monte‑Carlo Approach for Electricity Consumption Forecasting in Pakistan, with Heat‑Sensitivity Analysis (revised manuscript, Manuscript ID: NXSUST‑D‑26‑01053).

Short GitHub repository sidebar description
Reproducible research package for A Hybrid PSO‑Ensemble and Monte‑Carlo Approach for Electricity Consumption Forecasting in Pakistan, with Heat‑Sensitivity Analysis. Contains all raw and processed datasets, full Python source code for hybrid PSO‑ensemble forecasting, Monte‑Carlo uncertainty simulation and heat‑sensitivity analysis, to reproduce all manuscript tables and figures.
