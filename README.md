# Government Debt and Economic Growth in Canada

**Chris Perfitt | Independent Economics Research | Trent University**

An empirical investigation of the relationship between Canada's government debt-to-GDP ratio and economic growth, with particular attention to nonlinear relationships, productivity, and major economic crises.

This project uses R to combine economic datasets, estimate linear and quadratic regression models, examine lagged debt relationships, and compare the estimated relationship across economic environments. The accompanying R Markdown file documents the analysis through code, regression tables, diagnostic tests, and figures.

## Research question

How is government debt associated with Canadian economic growth, and does that relationship vary with debt levels, productivity growth, and the 2008–09 and 2020–21 crises?

The analysis examines both aggregate real GDP growth and real GDP per capita growth. Lagged debt specifications explore whether earlier debt levels are associated with subsequent growth; they do not, by themselves, establish causality.

## Data

The code combines four CSV inputs by country and year:

| File | Contents and expected format |
| --- | --- |
| `CADPOP.csv` | Population data; skips the first 10 rows and selects `All ages` from the `Age group 3 6` column. |
| `CADMFP.csv` | Multifactor productivity data; skips the first 10 rows, selects `Total economy 3`, and averages observations within each year. |
| `CADGDP.csv` | GDP data; selects Canada, GDP at market prices, and chained 2017 dollars using the `GEO`, `Estimates`, and `Prices` columns. |
| `CAD DEBT DATA.csv` | Debt-to-GDP data; selects `COUNTRY == "Canada"` and reshapes four-digit year columns into annual observations. |

The research concerns 1990–2023, but the script does not explicitly restrict the sample to those years. Actual coverage depends on the input files, their overlapping years, and observations lost when calculating growth rates and lags. Original CSV exports are needed because the import steps rely on their specific layouts.

## Methods

- **Data preparation:** Join annual series and construct GDP growth, GDP per capita, productivity growth, debt lags, and crisis indicators.
- **Baseline models:** Estimate linear and quadratic OLS specifications and calculate quadratic turning points.
- **Lagged models:** Examine one- and two-year debt lags.
- **Interaction models:** Allow the lagged debt relationship to vary with productivity growth and crisis indicators.
- **Marginal effects:** Calculate observation-level effects and scenario estimates using the delta method with HAC covariance estimates.
- **Diagnostics:** Include augmented Dickey–Fuller testing, Breusch–Pagan and Breusch–Godfrey tests, Ramsey RESET, and joint tests of debt terms.
- **Additional analysis:** Compare GDP per capita models, alternative productivity controls, 12-year rolling turning points, pre/post-2008 models, and 10-year rolling correlations.
- **Exploratory extension:** Use residuals from productivity regressions as controls in further growth models.

The variable `delta_mfp` is calculated as `100 * diff(log(MFP))`, an approximation to percentage productivity growth. The indicator named `d_2021` covers **both 2020 and 2021**.

## Code

`Appendix 01-27.Rmd` is the original analysis notebook. Its sections share objects, so run it from the beginning in order. Tables and figures are produced within the notebook; the script does not separately export them as image or CSV files.

**Tools:** R, R Markdown, dplyr, tidyr, ggplot2, lmtest, sandwich, car, urca, stargazer, and zoo.

## Running the analysis

### 1. Install dependencies

The following includes every package explicitly loaded by the current notebook, plus the rendering package:

```r
install.packages(c(
  "readr", "dplyr", "tidyr", "stringr", "ggplot2", "broom",
  "plm", "purrr", "stargazer", "ggforce", "ggrepel", "scales",
  "strucchange", "lmtest", "sandwich", "ARDL", "zoo", "tibble",
  "knitr", "car", "kableExtra", "urca", "rmarkdown"
))
```

Use R 4.1 or later because the notebook uses the `\(x)` anonymous-function syntax.

### 2. Configure the input files

Place the four CSV files in a `data/` folder beside the notebook. Update the four import paths from `/Users/chris/Desktop/...` to the corresponding relative paths:

```r
"data/CADPOP.csv"
"data/CADMFP.csv"
"data/CADGDP.csv"
"data/CAD DEBT DATA.csv"
```

Keep the existing import options, including `skip = 10` where used. These path changes are required in the original notebook before another computer can run it.

### 3. Run or render

Open the notebook in RStudio and execute the chunks sequentially. To render its configured PDF output, use an environment with XeLaTeX installed:

```r
rmarkdown::render("Appendix 01-27.Rmd")
```

For an HTML output without the PDF LaTeX requirement:

```r
rmarkdown::render(
  "Appendix 01-27.Rmd",
  output_format = rmarkdown::html_document()
)
```

## Interpretation and reproducibility notes

This is an observational, single-country analysis. Estimated turning points are features of fitted quadratic models, not recommended debt targets. Crisis-specific estimates depend on very few annual observations, and the productivity residuals are not independently identified exogenous shocks.

The notebook uses `sandwich::vcovHAC()` for selected inference calculations. Most `stargazer()` tables retain conventional OLS standard errors unless robust standard errors are explicitly supplied; HAC inference is not applied uniformly to every displayed table.

Two early GDP-growth charts multiply growth by 10 but divide the secondary axis by 5. Their axis transformation should be corrected before interpreting or reusing those figures.

This README was prepared by inspecting the source code. The analysis has not been rerun against the original CSV inputs, and the notebook's ADF helper and full rendering workflow still require execution checks. Numerical results should be verified against the accompanying research paper after reproducing the analysis
