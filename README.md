[README_maxsharpe.md](https://github.com/user-attachments/files/27756809/README_maxsharpe.md)
# Kernel-IQ Maximum-Sharpe Portfolio Experiment

This repository contains a reproducible empirical study of Kernel-IQ covariance estimation in a long-only maximum-Sharpe portfolio setting. The workflow tunes Kernel-IQ parameters in sample, applies the fitted estimator to monthly out-of-sample portfolio backtests, compares it with benchmark covariance estimators, and summarizes performance, ranking, Sharpe-ratio, and concentration diagnostics across optimizer seeds.

## Use of AI Assistance

OpenAI ChatGPT, using the GPT-5.5 Thinking model, was used as a development assistant during the preparation of this repository. Its role included helping to refactor the codebase, simplify the command-line workflow, prepare Linux execution scripts, debug implementation issues, draft documentation, and structure the analysis-pack outputs used to summarize the experimental results.

The underlying research design, methodological choices, data selection, empirical interpretation, and final validation of the code and results remain the responsibility of the author.

## Repository contents

- `run_project.py` runs the full single-machine ensemble workflow.
- `run_ensemble.py` runs the seed-level pipeline.
- `run_wiq.py` runs one seed through tuning, out-of-sample evaluation, ranking, and diagnostics.
- `aggregate_ensemble.py` aggregates seed-level outputs.
- `build_analysis_pack.py` creates a compact ZIP bundle for downstream analysis.
- `scripts/run_full_study.sh` runs the study in one terminal.
- `scripts/run_full_study_5x20.sh` runs 100 seeds in five parallel batches on Ubuntu/GNOME.

## Data files

This repository includes the expected input files:

```text
prices_multi_asset_master.csv
DGS3MO_monthly_rf.csv
```

The price file is included as a blank template: it retains the required dates and asset column headers, but the numerical price values have been removed. The empirical price values used in the associated study were sourced from Bloomberg Terminal and cannot be redistributed here because of licensing restrictions. To reproduce the empirical study, supply compatible price values in `prices_multi_asset_master.csv`.

The risk-free-rate file, `DGS3MO_monthly_rf.csv`, is included with its numerical values because it was sourced from a publicly available series rather than from Bloomberg Terminal.

The code expects price levels rather than precomputed returns. It computes returns internally.

## Setup

From the repository root, run:

```bash
bash scripts/setup_linux_venv.sh
source .venv/bin/activate
```

## Running the study in one terminal

```bash
bash scripts/run_full_study.sh 100
```

The optional argument is the number of seeds. The default seed range starts at 1000.

## Running the 100-seed five-batch study

On Ubuntu/GNOME, run:

```bash
bash scripts/run_full_study_5x20.sh
```

This creates a timestamped run folder on the Desktop, launches five terminal windows, runs seeds 1000 to 1099 in five batches, merges the seed folders, aggregates the ensemble, and builds a compact analysis pack.

## Manual commands

The main workflow can also be run manually:

```bash
python3 run_project.py --fresh --seed-start 1000 --num-seeds 100
python3 build_analysis_pack.py
```

## Sharpe-test modes

The default run uses a lighter Sharpe-test mode to keep large seed ensembles practical. The available modes are:

```bash
--sr-mode off
--sr-mode light
--sr-mode full
```

The full mode performs the heavier rolling-bootstrap layer and is intended for targeted verification rather than routine large ensemble runs.

## Main outputs

The main output tree is:

```text
outputs/
  ENSEMBLE_RUNS/
    seed_1000/
      OOS_results/
      ranking_results/
      SR_test_results/
      wiq_params.json
      gs_opt_params.json
      sre_opt_params.json
      wiq_is_best_diagnostics.csv
    _ensemble_outputs/
```

Compact analysis bundles are written under:

```text
analysis_packs/
```

## Configuration notes

The default settings are configured for a 10-asset monthly price file. Larger universes can be explored by supplying a different price file and corresponding configuration overrides, but lookback and scaling-window choices should be reviewed carefully before doing so.
