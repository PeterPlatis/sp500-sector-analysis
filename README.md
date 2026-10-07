# S&P 500 Sector & Valuation Analysis (Excel)

Business Analytics course project – Department of Industrial Management & Technology, University of Piraeus
Author: **Peter Platis**

An end-to-end analysis of the 505 companies in the S&P 500 index, done entirely in Microsoft Excel: data import and cleaning in Power Query, descriptive analytics with pivot tables, and a multiple linear regression model that estimates a company's market capitalization from its financial metrics.

## Questions

1. How is market value distributed across sectors, and how do sectors differ in valuation (P/E) and dividend yield?
2. Can a company's financial metrics explain how large the market values it?

## Data

- **Source:** [S&P 500 Companies with Financial Information](https://www.kaggle.com/datasets/franoisgeorgesjulien/s-and-p-500-companies-with-financial-information) (Kaggle), Open Data Commons PDDL licence
- **Size:** 505 companies × 14 fields (sector, price, P/E, dividend yield, EPS, 52-week range, market cap, EBITDA, P/S, P/B)
- **Type:** cross-sectional (one snapshot in time, not a time series), so "prediction" here means estimating the market cap you would expect for a company with given fundamentals. The data is a historical snapshot, not current market data.

## Tools

Microsoft Excel: **Power Query**, **PivotTables & PivotCharts**, **slicers**, Pareto and combo charts, **Analysis ToolPak** (Correlation, Regression), manual MSE / MAPE calculation.

## Data cleaning

- **Imported with Power Query** (UTF-8 encoding check) and rescaled market cap and EBITDA from dollars to billions.
- **Found a data-quality error:** the *52 Week Low* and *52 Week High* columns were swapped in every record (the "low" was always above the "high"). With the original labels, 0 of 505 current prices fell inside the range; after swapping, 472 of 505 did. Fixed by renaming the columns.
- **Missing values handled deliberately:** 2 missing P/E and 8 missing P/B values (< 2% of rows). They are not missing at random: P/E is undefined when earnings are negative and P/B when book equity is not positive. Filling them with the mean would treat those companies as typical, so the 10 rows were excluded from the regression (495 observations) and kept in the descriptive analysis (505).

## Descriptive findings

![Pareto analysis of market capitalization by sector](images/pareto_market_cap_by_sector.png)

- Total market cap of the 505 companies: **$24,865.5 bn**.
- The index is highly concentrated: **Information Technology alone is 27.1%**, the top three sectors exceed 50%, and **6 of 11 sectors account for 85.0%** of total value.
- Average P/E is **24.81** and average dividend yield **1.90%**. Valuations are highest in Energy (38.56) and IT (37.18) and lowest in Telecom (11.19) and Utilities (16.85).
- Dividend yield moves broadly **inversely to valuation**: Telecom pays 7.57% and Real Estate 3.89%, while Health Care and IT stay below 1.25%, consistent with growth companies reinvesting and mature companies distributing earnings.
- Sectors rank high for different reasons: Consumer Discretionary has the most companies (84) but an average size of $38.25 bn, while Consumer Staples reaches $2,087.0 bn with only 34 companies thanks to an average size of $61.38 bn.

Two pivot tables share a **sector slicer**, so filtering one sector updates both tables and their charts at once.

## Predictive model

**Dependent variable:** market capitalization ($ bn). **Method:** multiple linear regression with **backward elimination** at the 5% significance level, all models fitted on the same 495 observations.

- **Correlation matrix:** EBITDA is the only strong predictor (r = 0.771), followed by share price (r = 0.408). Valuation ratios have negligible correlation with size, as expected for normalized measures.

| Model | Predictors | R² | Adjusted R² | MSE | MAPE |
|---|---|---|---|---|---|
| 1 | 7 (all) | 0.6918 | 0.6873 | 2530.1 | 77.6% |
| 2 | 5 (dropped P/B, p = 0.770; dividend yield, p = 0.193) | 0.6907 | 0.6875 | 2539.0 | 74.6% |
| 3 | 4 (also dropped P/S, p = 0.100) | 0.6890 | 0.6864 | 2553.1 | 75.3% |

**Final model (3):** EBITDA, earnings per share, share price and P/E, all significant at 5%.

- EBITDA alone gives R² = 0.594; the other six variables add only about 10 percentage points.
- From Model 1 to Model 2, R² falls slightly but **adjusted R² rises**, showing the two dropped variables added no real information.
- The P/E p-value moves from 0.051 to 0.030 to 0.017 as correlated variables are removed, a sign of **multicollinearity**, which is why elimination was done step by step rather than from the full model's p-values alone.

## Conclusions & limitations

- Fundamentals explain about **69%** of the differences in market cap, with operating profitability (EBITDA) dominating. The 4-variable model performs essentially as well as the full 7-variable one.
- Percentage errors are high (MAPE ≈ 75%), so the model captures broad differences in company size rather than precise valuations of individual companies.
- The data is a single historical snapshot, so the results describe the index at that point in time.

## Files

| File | Description |
|---|---|
| `SP500_Analysis.xlsx` | Excel workbook: Power Query table, pivot tables & charts with slicer, Pareto table, regression outputs, forecasts and model comparison |
| `SP500_Analysis_Report_GR.pdf` | Full written report (in Greek) |
| `data/constituents-financials.csv` | Original dataset |
| `images/` | Charts used in this README |

## Use of AI tools

As disclosed in the report, AI tools were used for guidance on Power Query, the Analysis ToolPak and the MSE/MAPE calculations, and for help with academic wording. The analysis decisions were checked against the data.
