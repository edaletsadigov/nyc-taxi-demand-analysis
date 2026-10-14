# Notebook: `nyc_analysis.ipynb`

Hourly and daily demand analysis of 1,458,644 NYC taxi trips (January–June 2016). This page explains what the notebook contains, which libraries it uses, and **why each one was chosen**.

- **Notebook:** [`nyc_analysis.ipynb`](nyc_analysis.ipynb) (executed, all outputs saved, no errors)
- **Data:** `NYC.csv`, described in [`../data/README.md`](../data/readme.md). The file is ~200 MB, is **not** stored in this repository, and must be placed **in this folder** (next to the notebook) before running, because the notebook reads it with `pd.read_csv('NYC.csv')`.
- **Charts produced:** 9, exported as PNG in [`../charts/`](../charts/readme.md)
- **Results and recommendations:** [`../README.md`](../README.md)

---

## 1. Notebook structure

| Section | Content |
|---|---|
| **Libraries** | One import cell, global style, colours, ordered weekday/month lists |
| **1. Data Overview and Cleaning** | Shape, `info()`, `describe()`, missing values, duplicates, `nunique()`; datetime parsing; consistency check (`trip_duration` vs `dropoff − pickup`); seconds → minutes; outlier analysis (by vendor); 1–120 min filter with before/after counts; impact on mean/median/skew; feature extraction (`hour`, `weekday`, `month`, `is_weekend`, `date`) with verification |
| **2. Analysis** | 2.1 trips by hour · 2.2 trips by weekday · 2.3 duration by hour · 2.4 duration distribution · 2.5 monthly trips · 2.6 hour × weekday heatmap · 2.7 weekday vs weekend profile · 2.8 vendor comparison · 2.9 daily series and anomalies |
| **3. Statistical Analysis** | 3.1 vendor duration · 3.2 weekday vs weekend duration · 3.3 duration across months · 3.4 duration across hours · 3.5 vendor share by hour · 3.6 hourly demand vs duration · 3.7 weekday effect on daily demand · summary table with Bonferroni correction |
| **4. Final Summary** | Key findings, 4 operational insights, recommendations, differences from the task's reference notes, limitations |

Every chart is followed by a printed `Takeaway:` line computed from the data, and every cleaning decision is written down with its count, share and reason.

---

## 2. Libraries: what is used and why

| Library | Used for | Why this library |
|---|---|---|
| **pandas** | Loading the CSV (`read_csv`); parsing dates (`to_datetime` with an explicit format); the `.dt` accessor to extract hour, weekday and month; `groupby` + named aggregation; `pivot_table` (heatmap data); `crosstab` (vendor × hour); `Categorical` (ordered weekday and month labels); `rolling` (7-day mean); `merge`, `nunique`, `describe`, `quantile` | The core tool for tabular, time-based data. The whole task is datetime extraction and aggregation, and pandas does both in vectorised form on 1.4 million rows without loops |
| **numpy** | `np.where` to label hours as Peak / Normal / Off-peak; `np.sqrt` for Cramér's V | Fast element-wise conditions and maths on whole columns; the labelling is a vectorised alternative to an `if/else` loop |
| **matplotlib** | Figure and axes control (`plt.subplots`), shaded windows (`axvspan`), mean/median reference lines (`axvline`), bar labels (`bar_label`), thousands-separator axis formatting (`FuncFormatter`) | Gives full control over titles, axis labels, annotations and layout, so every chart can carry a title, labelled axes and a visible takeaway |
| **seaborn** | `sns.set_theme` (consistent grid style), `lineplot`, `barplot`, `histplot`, `heatmap` | Statistical charts with less code and cleaner defaults than raw matplotlib; the heatmap of 168 hour × weekday cells is one call |
| **scipy.stats** (`from scipy import stats as st`) | `mannwhitneyu` (two groups), `kruskal` (3+ groups), `chi2_contingency` (categorical × categorical), `spearmanr` (monotonic correlation) | Trip duration is right-skewed (skew 2.21), so rank-based tests are the appropriate choice; with 1.4M rows, scipy's tests still complete quickly. A single alias `st` is used throughout |

### Why these choices and not others

- **Rank-based tests instead of t-tests:** the duration distribution is strongly skewed, so means are unreliable. Mann-Whitney and Kruskal-Wallis compare ranks and are robust to outliers.
- **Effect sizes next to every p-value:** with 1.4 million trips almost any difference is "significant". The notebook reports rank-biserial *r*, epsilon-squared, Cramér's V and Spearman ρ so that *significant* is not confused with *useful*.
- **Bonferroni correction:** eight tests are run, so the significance threshold is adjusted to 0.05 / 8 = 0.0063.
- **Day-level unit for demand tests:** demand varies by calendar day, not by trip, so weekday differences in trip counts are tested on 182 daily counts (26 per weekday).
- **Matplotlib/seaborn instead of Plotly:** these are the libraries required by the task, and they export static PNGs that render directly on GitHub.
- **No loops over rows:** all computations are vectorised (`groupby`, `np.where`, boolean masks); `iterrows` is never used. The only loops are over a handful of items for printing.

---

## 3. Key conventions in the code

- `raw` (untouched file) → `df` (typed copy) → `clean` (filtered, with new features). Raw data is never overwritten.
- Colours: blue `#2E86AB` and orange `#F26419`, used consistently.
- Derived columns have explicit names with units: `trip_duration_min`, `trips_share_%`, `busy_car_hours`.
- Fixed `alpha = 0.05`; each test prints H0, H1, n, group statistics, p-value, effect size and a plain-language conclusion.
- Two small helper functions only: `rank_biserial()` (effect size for Mann-Whitney) and `fmt_p()` (readable p-values).

---

## 4. How to run

1. Install dependencies (from the repository root):
   ```bash
   pip install -r requirements.txt
   ```
2. Download `NYC.csv` (see [`../data/README.md`](../data/readme.md)) and put it **in this folder**.
3. Open `nyc_analysis.ipynb` in Jupyter or VS Code and use *Run All*. Reading the 200 MB CSV and the tests on 1.4M rows are the slowest steps, so allow a few minutes.

---

## 5. Limitations of the notebook

- The data covers **six months only**; "seasonality" is reported as a Jan–Jun pattern.
- Only pickups are observed: unmet demand, cancellations and fares are unknown.
- Coordinates, weather and traffic are not analysed; explanations that involve them (for example the 23–24 January drop) are assumptions.
- The 1–120 minute filter follows the task definition and removes 0.74% of rows; it is a convention, not a data-derived threshold.

