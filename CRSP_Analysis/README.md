# CRSP Empirical Analysis

This folder contains the empirical application of the thesis, based on high-dimensional canonical correlation analysis (HDCCA) applied to CRSP stock returns. 

The goal is to study whether there is a stable and economically meaningful dependence structure between cyclical and defensive equity panels.

## Main Notebook

The main notebook is:  `hdcca_empirical_analysis_crsp.ipynb`

It performs the full empirical workflow:

1. Loads CRSP daily stock data from the zipped raw dataset.
2. Maps stocks into cyclical and defensive groups using an FF12-inspired SIC industry classification.
3. Converts daily returns into weekly returns.
4. Selects a balanced panel of stocks with high coverage over the sample.
5. Estimates rolling-window CCA between the cyclical and defensive panels.
6. Compares empirical canonical roots with HDCCA bulk benchmarks and permutation-null benchmarks.
7. Computes stability diagnostics, including split-sample vector overlap, top-k subspace overlap, eigengaps, and out-of-sample roots.
8. Exports cleaned data, summary tables, and figures used in the thesis.

The baseline specification uses 80 cyclical stocks and 80 defensive stocks over 832 complete weekly observations, from 2010-01-08 to 2025-12-12. The main rolling analysis uses 260-week windows, advanced in 4-week steps, producing 144 rolling windows.

## Data

The raw data file is: `crsp_dataset.zip`

This archive contains one large CRSP daily stock file. The notebook reads the data directly from the zip file. The raw file is not needed to understand the empirical results because the notebook exports cleaned and aggregated objects into `empirical_outputs_crsp/`.

The cleaned data exports are in: `empirical_outputs_crsp/clean_data/`

Key files:

- `returns_weekly_clean_crsp.csv`: wide weekly return matrix for the selected stocks. Rows are weeks; columns are selected securities.
- `stock_metadata_clean_crsp.csv`: metadata for the selected stocks, including identifiers, ticker, exchange, SIC code, FF12 sector, cyclical/defensive bucket, coverage, and market-capitalization measures.
- `weekly_long_selected_universe_crsp.csv`: long-format weekly panel with returns, market capitalization, bucket, sector, SIC code, and stock identifiers.

These cleaned files are derived from CRSP and are intended to document the empirical construction of the selected universe.

## Empirical Design

The analysis divides stocks into two economically motivated panels:

- Cyclical stocks: industries more sensitive to the business cycle.
- Defensive stocks: industries expected to be more stable across the business cycle.

CCA is then used to find linear combinations of cyclical and defensive returns that are maximally correlated. Because the panels are high dimensional relative to the rolling sample size, large in-sample canonical roots may arise mechanically from noise. The notebook therefore evaluates each rolling window using several diagnostics:

- Leading root: the largest squared sample canonical correlation.
- HDCCA bulk edge: the theoretical high-dimensional noise benchmark for the sample dimensions.
- Permutation null: an empirical benchmark obtained by breaking cross-panel dependence.
- Outlier count: the number of sample roots above the bulk edge.
- Eigengap: separation between adjacent sample roots.
- Split-sample stability: whether estimated canonical directions replicate across two subsamples.
- Top-k subspace stability: whether the leading canonical subspaces replicate across subsamples.
- Out-of-sample root: whether the in-sample canonical direction still produces correlation on held-out observations.

The central interpretation rule is that a large in-sample root alone is not enough. A credible low-rank structure should also show stability and out-of-sample persistence.

## Results

All exported results are stored in:

- `empirical_outputs_crsp/tables/`
- `empirical_outputs_crsp/figures/`

### Main Tables

- `baseline_sample_summary.csv`: baseline sample dimensions, dates, rolling-window settings, HDCCA benchmark, and panel sizes.
- `crsp_preselection_summary.csv`: summary of the initial candidate stocks by bucket.
- `crsp_selected_panel_summary.csv`: final balanced panel summary.
- `crsp_selected_bucket_sector_mix.csv`: sector composition of the selected cyclical and defensive panels.
- `main_260w_rolling_results_master_table.csv`: window-level results for the main 260-week rolling specification.
- `main_260w_rolling_spectra_long.csv`: long-format rolling canonical spectrum.
- `main_260w_rolling_summary_metrics.csv`: aggregate summary statistics for the main rolling analysis.
- `main_260w_credible_leading_windows.csv`: windows satisfying the credibility criteria, if any.
- `full_sample_static_spectrum.csv`: canonical spectrum estimated on the full selected sample.
- `robustness_summary_across_specs.csv`: comparison across the baseline, alternative window lengths, and pre/post-2020 subsamples.
- `pre2020_260w_*`, `post2020_260w_*`, `robust_208w_*`, `robust_364w_*`: robustness outputs for subsamples and alternative window lengths.
- `baseline_written_finding.txt`: short written summary of the baseline finding.

### Main Figures

- `figure1_leading_root_vs_bulk_and_null_main260.png`: leading root compared with the HDCCA bulk edge and permutation-null benchmark.
- `figure2_outlier_count_main260.png`: number of roots above the HDCCA bulk edge across rolling windows.
- `figure3_split_sample_stability_component1_main260.png`: split-sample stability of the first canonical direction.
- `figure4_topk_subspace_stability_main260.png`: stability of leading top-k canonical subspaces.
- `figure5_eigengap_diagnostics_main260.png`: eigengap diagnostics for spectral separation.
- `figure6_phase_transition_diagnostics_main260.png`: diagnostics for windows that meet the credibility criteria.
- `figure7_insample_vs_heldout_main260.png`: comparison of in-sample and held-out canonical roots.
- `figure8_robustness_summary_across_specs.png`: robustness comparison across specifications.
- `figure_full_sample_static_spectrum.png`: full-sample canonical spectrum.

## Main Takeaway

The empirical results show strong in-sample spectral dependence: the leading canonical roots are large and usually exceed the HDCCA bulk benchmark. However, the estimated canonical directions are weakly stable across splits and their held-out performance is much smaller than their in-sample fit. This supports the thesis argument that HDCCA is useful as a diagnostic framework, but in-sample canonical roots must be interpreted together with stability and out-of-sample validation.

## Reproducibility Notes

To reproduce the analysis, run `hdcca_empirical_analysis_crsp.ipynb` from this folder with the raw `crsp_dataset.zip` present. The notebook writes all derived outputs to `empirical_outputs_crsp/`.

The raw CRSP data are not documented in detail here because access is restricted. The exported cleaned data, tables, and figures provide enough information to understand the sample construction, diagnostics, and empirical conclusions without opening the restricted raw file.