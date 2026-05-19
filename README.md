# High-Dimensional CCA Thesis Code

This repository contains the code, thesis source, simulation outputs, and empirical analysis for the Bachelor Thesis:

*"High Dimensional Canonical Correlation Analysis: Applications to Financial Data"*  by Alba Torres. 

The project studies high-dimensional canonical correlation analysis (HDCCA) in two settings:

1. Monte Carlo simulations that illustrate how canonical roots behave when the number of variables is large relative to the sample size.
2. An empirical application using CRSP stock returns, where cyclical and defensive equity panels are compared through rolling-window CCA diagnostics.

The thesis PDF and LaTeX source are included so that the code can be read together with the written methodology and interpretation.

## Repository Structure

- `Editor Econ/`: LaTeX thesis source, bibliography, figures/tables used in the document, and the compiled `memoria.pdf`.
- `Simulations/`: Monte Carlo simulation notebook and README explaining the simulated data-generating processes, experiments, diagnostics, and exported results.
- `CRSP_Analysis/`: empirical application on CRSP data, including the main notebook, cleaned/exported outputs, figures, tables, and a folder-specific README.
- `Literature/`: papers and notes used during the literature review. These are not uploaded due to heavy files; yet references can be found in the main paper.
- `Empirical_Analysis_v1/`: earlier empirical-analysis version kept for development history.

## Main Code Files

- `Simulations/simulations.ipynb`: runs the Monte Carlo experiments used to study pure-noise behavior, phase transitions, spike strength, multiple spikes, diffuse dependence, and finance-style robustness checks.
- `CRSP_Analysis/hdcca_empirical_analysis_crsp.ipynb`: runs the final empirical application using CRSP daily stock data, converted to weekly returns and split into cyclical and defensive panels.
- `Editor Econ/memoria.tex`: main LaTeX source for the written thesis.

## Data Access

The Monte Carlo simulations are fully reproducible from the code in `Simulations/`.

The CRSP empirical application uses restricted CRSP data. The raw data file is therefore not intended for public redistribution. To make the analysis understandable without accessing the raw data, `CRSP_Analysis/` includes:

- a detailed `README.md`,
- cleaned/derived output files,
- exported figures,
- exported summary tables,
- and the notebook code documenting the full pipeline.

Someone without CRSP access can still inspect the methodology, diagnostics, selected output tables, and final figures.

## Methodological Overview

The project evaluates whether large sample canonical correlations should be interpreted as genuine cross-panel dependence. In high-dimensional settings, classical CCA can produce large in-sample roots even when the two panels are independent. For this reason, the thesis uses a joint diagnostic rule:

- compare leading roots with high-dimensional bulk and noise benchmarks,
- check whether canonical directions are stable across samples,
- evaluate whether the estimated directions retain explanatory power out of sample.

This logic is used both in the simulations and in the CRSP empirical application.

## Outputs

The main outputs are:

- Monte Carlo tables and figures exported from `Simulations/simulations.ipynb`.
- Empirical figures and tables in `CRSP_Analysis/empirical_outputs_crsp/`.
- LaTeX-ready thesis figures/tables in `Editor Econ/imagenes/`.
- The compiled thesis PDF at `Editor Econ/memoria.pdf`.

## Reproducing the Work

To reproduce the Monte Carlo analysis, open and run:

```text
Simulations/simulations.ipynb
```

To reproduce the empirical analysis, CRSP access is required. With the raw CRSP file available in `CRSP_Analysis/`, open and run:

```text
CRSP_Analysis/hdcca_empirical_analysis_crsp.ipynb
```

To rebuild the thesis PDF from the LaTeX source, run from `Editor Econ/`:

```text
latexmk -pdf -interaction=nonstopmode -halt-on-error memoria.tex
```

## GitHub

Repository link:

```text
https://github.com/albats/hdcca_thesis
```
