# Income vs Tax Burden
## A Decade of Federal Tax Distribution (2012–2022)

**[📊 View the interactive dashboard on Tableau Public →](https://public.tableau.com/app/profile/wonseok.lee8183/viz/CapstoneProjectIncomevsTaxBurden_WonseokLee/CapstoneDashboard)**

---

## Project Overview

This project analyzes federal income tax burden across U.S. states and Adjusted Gross Income (AGI) brackets using IRS Statistics of Income (SOI) Historic Table 2 data covering Tax Years 2012–2022.

I picked this dataset because federal tax distribution touches every working person in the U.S., and the past decade has been packed with policy events worth measuring — TCJA in 2018, COVID stimulus in 2020, ARPA's EITC expansion in 2021. The question I kept coming back to: did these policies actually move the numbers, and if so, for whom?

### Research Questions

1. How do effective tax rates (ETR) vary across states and income brackets?
2. Did the Tax Cuts and Jobs Act (TCJA) significantly change tax burden distribution, and which income groups benefited most?
3. How did COVID-19 stimulus and ARPA's expanded EITC affect federal tax patterns?
4. Are state-level tax disparities driven by tax law or by income composition?

---

## Dataset

- **Source:** [IRS Statistics of Income (SOI) Historic Table 2 – State Data](https://www.irs.gov/statistics/soi-tax-stats-historic-table-2)
- **Coverage:** 50 states + DC + PR + Other Areas
- **Years:** 11 tax years (2012–2022)
- **Granularity:** State × AGI bracket × Year
- **Raw size:** 6,468 rows × 196 columns
- **Final analysis dataset:** 6,468 rows × 18 columns (after column subset, renaming, and calculated field generation)

---

## Key Findings

1. **Progressive taxation confirmed** (Pearson r = 0.96, p < 0.001). ETR rises from approximately -0.1% (Under $1) to ~25% ($1M+).
2. **TCJA reduced ETR** by an average of -1.32 pp across all brackets (paired t-test, p < 0.0001).
3. **The biggest TCJA winner wasn't who I expected.** I assumed the $1M+ bracket would dominate. The data said otherwise — $200K–$500K got the largest cut at -2.94 pp, while $1M+ saw only -1.84 pp. The smaller cut at the top makes sense once you remember capital gains are taxed at 20%, not the 37% marginal rate that applies to wages.
4. **Federal ETR doesn't actually vary across states** (one-way ANOVA, p = 0.88). The map looks like a geography story, but it isn't. High-ETR states include both NY/CA (high state tax) and WA/TX/FL (no state income tax) — what they share is a concentration of high earners. This is a Simpson's Paradox: the state-level pattern dissolves once you control for income mix.
5. **COVID-19 had no detectable effect on federal ETR** (p = 0.93). That tracks — pandemic relief was delivered through credits and direct payments, not rate changes. If you're measuring policy through ETR alone, you'd miss the entire 2020 fiscal response.
6. **ARPA 2021 EITC expansion** added 5.5M recipients (32.07M total), then expired in 2022 (-8.4M, returning to 23.69M). The visible "credit cliff" demonstrates policy whiplash for working-class taxpayers.
7. **Linear regression model** (state-level aggregate Tax ~ AGI) explains 80.6% of variance (R² = 0.806), with marginal tax rate ≈ 16.3%.

---

## Methodology

### ETL Pipeline (Python)
- Ingested 11 raw IRS files of mixed format (10 CSV, 1 XLSX with non-standard header offset)
- Standardized inconsistent column conventions (lowercase 2012–2013 vs. uppercase 2014–2022)
- Handled comma-formatted numeric strings, IRS footnote suppression markers (`[19]`), and accommodated structural change in 2018 (state count 53 → 54)
- Validated aggregation integrity (sum of brackets 1–10 reconciles to bracket 0 within 0.001%)

### Statistical Analysis
- **Pearson correlation** (income bracket ↔ ETR)
- **One-way ANOVA** (ETR variance across states)
- **Independent and per-bracket t-tests** (pre-TCJA vs. post-TCJA)
- **Time-period t-tests** (COVID-19, ARPA effects)
- **Linear regression** (Tax ~ AGI at state-bracket-year level)

### Visualization
- **Python (matplotlib):** 7 publication-style PNG charts for the EDA document
- **Tableau Public:** 5-sheet interactive dashboard with reference bands, choropleth mapping, calculated fields (ETR, TCJA pp Change), and tooltips

### Tableau-Ready Dataset Preparation
- Subset 196 raw columns → 14 analysis columns + 4 derived fields (ETR, Bracket_Label, TCJA_Period, EITC_Per_Recipient)
- Sortable bracket labels with numeric prefixes for correct ordering in Tableau

---

## Files in This Folder

| File | Description |
|---|---|
| `Capstone_Income_vs_Tax_Burden.ipynb` | Main Jupyter notebook (full analysis pipeline) |
| `Capstone EDA - Wonseok Lee.pdf` | 3-page EDA document (project Part 2 deliverable) |
| `visual_1_etr_by_bracket.png` | Bar chart: ETR by income bracket (2022) |
| `visual_2_etr_over_time.png` | Line chart: ETR trends 2012–2022, top 5 brackets |
| `visual_3_state_comparison.png` | Top/bottom 10 states by ETR |
| `visual_4_tcja_comparison.png` | Pre/Post TCJA grouped bar comparison |
| `visual_5_eitc_distribution.png` | EITC distribution by bracket |
| `visual_6_regression.png` | Tax-vs-Income regression model with R² annotation |
| `visual_7_eitc_trends.png` | 3-panel EITC time series (Total $, Recipients, Per Recipient) |

---

## Tools & Skills Demonstrated

**Python**
- `pandas`, `numpy` for ETL and data wrangling
- `scipy.stats` for hypothesis testing
- `sklearn.linear_model` for regression
- `matplotlib` for static visualization

**Tableau Public**
- Calculated fields (FIXED LOD expressions for cross-period comparison)
- Reference bands for policy-era highlighting
- Filled choropleth mapping with auto-detected geography
- Multi-sheet dashboard with consistent storytelling structure
- Custom number formatting (decimals, percentage points, currency)

**Statistics & Analysis**
- Hypothesis testing (t-tests, ANOVA, correlation)
- Linear regression and model evaluation
- Recognizing and explaining Simpson's Paradox in real-world tax data

**Data Engineering**
- Multi-file ingestion with format heterogeneity
- Schema reconciliation across 11 years of changing IRS conventions
- Data validation with explicit aggregation reconciliation
- Designing Tableau-ready datasets from raw analytical sources

**Domain Knowledge Applied**
- U.S. federal income tax structure and rate schedules
- TCJA, ARPA, and EITC mechanics
- Capital gains preference and its impact on top-bracket ETR
- Reading IRS data including suppression conventions

---

## Implications

A few patterns from this analysis worth highlighting:

- **Tax-burden maps look like geography stories. They aren't.** ANOVA shows federal ETR doesn't vary meaningfully across states (p = 0.88). What looks like regional disparity is income clustering. Anyone presenting state-level tax data without testing this assumption is telling the wrong story.
- **I went into TCJA expecting the $1M+ bracket to be the biggest winner.** The data said $200K–$500K. That changed how I think about who benefits from rate cuts — capital gains preference matters more at the top than the marginal rate does.
- **Stimulus mechanics shape what's measurable.** COVID-19 relief produced no detectable change in ETR because it was delivered through credits and direct payments, not rate changes. If your KPI is ETR, you'd conclude COVID had no fiscal effect — which is the wrong conclusion drawn correctly.
- **Temporary expansions create whiplash.** ARPA's 2021 EITC expansion expired in 2022, and 8.4M recipients lost access. The data shows what "sunset clauses" actually look like at the household level.

---

## Reproducibility

The notebook contains all data wrangling, calculated fields, statistical tests, and visualization code. The Tableau workbook is published with full filter and field definitions visible on Tableau Public.

To rebuild the Tableau-ready dataset locally:
1. Download all 11 raw files from the IRS SOI Historic Table 2 page
2. Run the notebook end-to-end (Phases 1–6)
3. Output: `IRS_tableau_ready.csv` (6,468 × 18)

---

## Acknowledgments

Project completed as part of the Virginia Tech × Fullstack Academy Data Analytics Bootcamp (Cohort 2511-FTB-CT-DAT-PT).
