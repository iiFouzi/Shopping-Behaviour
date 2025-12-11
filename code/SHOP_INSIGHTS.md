**Shop Notebook Insights and Summary

**Problem Observed**
- Running the Jupyter notebook cell-by-cell showed different `Age` frequency counts than running the full notebook with “Run All”. This caused confusion and undermined reproducibility.

**Root Causes**
- CSV parsed with the wrong delimiter in some runs (semicolon vs comma) causing column misalignment.
- `Age` was not coerced to numeric on load; parsing issues produced strings and NaNs.
- Numeric imputation code used `.astype(int)` after fill, collapsing values and creating artificial spikes in frequency counts.

**What I changed (files / edits)**
- `shop.ipynb` (edited):
  - Explicit CSV load with `sep=';'` to avoid delimiter ambiguity.
  - Coerced `Age` to numeric: `df['Age'] = pd.to_numeric(df['Age'], errors='coerce')`.
  - Added `df['Age_missing'] = df['Age'].isnull().astype(int)` to preserve provenance of imputed values.
  - Replaced `.astype(int)` after numeric imputation with `df[col] = df[col].fillna(mode)` to avoid collapsing values.
  - Added immediate debug prints after load: `df.head()`, `df.shape`, and `df['Age'].value_counts().head()`.

**How to reproduce / verify (Windows PowerShell)**
1. Restart the Jupyter kernel for a clean state.
2. Run → Restart Kernel, then Run → Run All.
3. Verify `Age` counts match single-cell runs by running this in a notebook cell after load:

```python
print('After load:', df.shape)
print('Missing Age:', df['Age'].isnull().sum())
print(df['Age'].value_counts().head())
```

Or from PowerShell (quick check):

```powershell
python - <<'PY'
import pandas as pd
pd.set_option('display.max_rows', 10)
df = pd.read_csv('shop_patterns.csv', sep=';')
df['Age'] = pd.to_numeric(df['Age'], errors='coerce')
print('Loaded shape:', df.shape)
print('Missing Age:', df['Age'].isnull().sum())
print(df['Age'].value_counts().head())
PY
```

**Key Findings**
- `Age` had a substantial missing fraction (~20%): prefer imputing with indicator columns rather than dropping rows.
- Mode imputation is simple but can create artificial spikes; keep missing-indicator columns to allow models to learn missingness patterns.
- For better imputation consider `Median`, `KNNImputer`, or `IterativeImputer` depending on downstream modeling needs.

**Recommendations / Next steps**
- Add `requirements.txt` (created) and a short `README.md` describing how to run the notebook and dependencies.
- Consider model-based imputation if Age distribution preservation is important.
- Add a tiny CI check (GitHub Actions) that loads `shop_patterns.csv` with `sep=';'` and confirms `df.shape` and that `Age` is numeric to catch regressions.
- Add a short cell documenting data provenance and why `Age_missing` was added.

**Suggested commit message**
- `fix(shop.ipynb): explicit CSV separator, coerce Age to numeric, add Age_missing indicator, avoid int casting in imputation`

**Suggested PR description**
Fix parsing/cleaning bugs causing inconsistent Age counts between single-cell runs and full notebook runs. Adds `Age_missing` indicator and safer numeric imputation. See notebook for reproduction steps and recommended next steps.

**License / attribution**
- No external data or copyrighted text added.

**Graphs Analysis**
- **Age Distribution (Histogram)**: Shows the overall distribution of customer ages. Interpretation: the distribution is roughly [describe shape after you run it — e.g., "right-skewed" or "approximately normal"]. Note: if mode imputation is used, expect a visible spike at the imputed age; the `Age_missing` indicator lets you separate imputed rows from original.

- **Imputation Comparison (4-panel histograms: original / mean / median / mode)**: Use this to visually assess how different imputation strategies change the distribution. Interpretation guidance:
  - If the mean/median imputed histograms closely resemble the original (ignoring NaNs), those methods preserve distribution better.
  - Mode imputation will concentrate many rows at a single value — this is acceptable when the variable is truly categorical-like but can bias models for continuous features.

- **Age Line Plot (`value_counts().sort_index()`)**: Good for showing discrete-age frequency trends. Interpretation: verify that the x-axis (age) is numeric; sorting by index ensures ages are in ascending order. If counts change after cleaning, it indicates rows were dropped, coerced, or rebinned.

- **Purchase Amount by Category (barplot)**: Shows which product categories contribute the most revenue. Interpretation: categories with highest bars are highest revenue; consider normalizing by number of purchases to find per-customer spend.

- **Purchase Amount Boxplot (after capping)**: Demonstrates the effect of IQR capping on outliers. Interpretation: capping reduces extreme whiskers — check summary stats (min, Q1, median, Q3, max) before/after to quantify impact.

- **Frequency of Purchases by Gender (stacked bar)**: Highlights behavioral differences. Interpretation: look for gender-specific peaks (e.g., more "Weekly" purchases in one group). If categories were imputed to `Unknown_Frequency`, consider excluding those rows from behavioral analyses.

- **Correlation Heatmap (numeric features)**: Quick check for collinearity and useful predictors. Interpretation: high correlation (|r| > 0.7) suggests redundant features; consider dimensionality reduction or dropping one of the correlated features.

Suggested figure captions (copy into notebook markdown above each plot):
- "Figure X — Age distribution (histogram). Shows distribution of `Age` and compares mean/median lines." 
- "Figure Y — Imputation comparison: original vs mean/median/mode imputed ages." 
- "Figure Z — Total purchase amount by category (bar chart)."

Suggested code snippets to save figures (add after each plot):
```python
# save current figure to file
plt.tight_layout()
plt.savefig('figures/age_distribution.png', dpi=150)
```

Notes on interpretation workflow:
- Always inspect the raw `df.head()` and `df.info()` before trusting a plot — parsing issues or wrong dtypes commonly produce misleading plots.
- When you show plots in a repo/README, include the short caption and a one-line interpretation (what the plot suggests about user behavior).
- If you add plots to GitHub, commit the image files under `docs/` or `figures/` and reference them in the notebook README or `SHOP_INSIGHTS.md`.

