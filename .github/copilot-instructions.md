## Quick orientation

This repo performs unsupervised analysis of monthly U.S. net electricity generation (2015–2024) inside a single notebook. The primary entry points and data are:

- Notebook: `model.ipynb` — the full analysis pipeline (data load, cleaning, exploratory plots, and unsupervised modeling).
- Dataset: `Net_generation_for_all_sectors.csv` — CSV used directly by the notebook; path is relative to the repo root.

## What an AI assistant should know first

- The notebook reads the CSV with `pd.read_csv('./Net_generation_for_all_sectors.csv', skiprows=4)` (there are header rows to skip).
- The script pivots the table by setting `description` as the index, then transposes (`df_t = df.T`) so rows are timestamps and columns are fuel series.
- Date parsing uses month-year format: `df_t.index = pd.to_datetime(df_t.index, format="%b %Y")` and the index is sorted with `df_t.sort_index()`.
- Common cleanup patterns: `df.drop(columns=["units", "source key"], errors="ignore")` and selecting columns that have >=80% coverage:
  - `valid_counts = df_t.notna().sum()`
  - `threshold = int(0.8 * len(df_t))`
  - `keep_cols = valid_counts[valid_counts >= threshold].index`

These are fundamental to avoid empty or misaligned vectors for clustering.

## Project-specific conventions and implicit knowledge

- Single-notebook analysis: development and experiments are kept in `model.ipynb`. Small edits to preprocessing should be mirrored in the notebook to keep results reproducible.
- Anchor series: the notebook notes that `United States : all fuels (utility-scale)` and `petroleum liquids` are fully populated and used as reference/anchor series — rely on these when aligning or sanity-checking data.
- Missing-value strategy: the workspace uses a coverage threshold (80%) rather than imputation by default; follow that unless a change is documented in the notebook.

## How to run locally (developer workflow)

1. Create a Python environment with recent packages: pandas, numpy, matplotlib, seaborn, plotly, statsmodels.
   - Example: `python -m venv .venv; .\.venv\Scripts\Activate.ps1; pip install pandas numpy matplotlib seaborn plotly statsmodels`
2. Open `model.ipynb` in Jupyter or VS Code and run cells top-to-bottom. The notebook uses only the CSV and common scientific Python packages.

## Typical debugging checks (examples from the notebook)

- Inspect raw table: `df.info()` and `df.head()`
- Check missingness: `df.isnull().sum()` and `df_t.isna().sum().sort_values(ascending=False).head()`
- Confirm date parsing: after `pd.to_datetime(...)`, inspect `df_t.index[:5]` and `df_t.index.freq` (if available)

## Integration points and external dependencies

- There are no external APIs; the only external input is the CSV file in the repo.
- If you add new data sources, document them in `README.md` and update the notebook paths.

## When editing the notebook or adding scripts

- Keep preprocessing deterministic and colocated near the top of `model.ipynb` so downstream cells (clustering, plotting) remain reproducible.
- If you convert notebook steps into scripts, preserve these function/variable names where practical: `df`, `df_t`, `df_clean`, `X` — tests and cells reference these names.

## What not to change without updating the notebook

- Do not change the CSV filename or its relative path without updating `model.ipynb` and `README.md`.
- Don't change the skiprows (4) without verifying header alignment.

## If you need more context

- Inspect `model.ipynb` top cells for the canonical preprocessing and the short markdown notes about missingness and anchor series.
- Ask the repo owner to add a `requirements.txt` or minimal `environment.yml` if you plan to run CI or share reproducible environments.

---
If any of these points are unclear or you want the assistant to add a `requirements.txt` and a lightweight runner script (`run_analysis.py`), tell me and I will create them and update this file accordingly.
