# I'll Cry in Another Walk-In

## Tracking Quit Rates in Accommodation and Food Services

**Author:** Jason Staats  
**Period:** January 2016 through September 2026  
**Updated:** November 2026

## Overview

This project examines monthly, seasonally adjusted quits rates in accommodation and food services using data from the U.S. Bureau of Labor Statistics Job Openings and Labor Turnover Survey.

The analysis tracks how quits rates have changed since the pandemic-era surge in voluntary separations and how recent observations compare with pre-pandemic levels. It also explores remaining seasonality, persistence, and the performance of several forecasting approaches.

The project serves as a companion to *Up or On the Rocks?*, which examines employment levels in the same sector.

## Data Source

- **Series:** JTS720000000000000QUR, Quits Rate, Accommodation and Food Services (NAICS 72)
- **Provider:** U.S. Bureau of Labor Statistics, Job Openings and Labor Turnover Survey (JOLTS)
- **Period:** January 2016 through September 2026
- **Frequency:** Monthly
- **Units:** Quits rate as a percentage of employment
- **Seasonal adjustment:** Applied by BLS
- **Access:** BLS Public Data API v2

The quits rate measures voluntary separations during the month as a percentage of employment. Layoffs are not counted as quits. The series can offer insight into workers’ willingness to leave their jobs without directly measuring their reasons for doing so.

The notebook checks that all 129 monthly observations in the analysis period are present, with no duplicate or unexpected dates.

JOLTS estimates are subject to revision. Rerunning the notebook can change historical observations and model results.

## COVID-19 Interpolation

The notebook replaces February through October 2020 quits rates with time-based linear interpolation between the January and November 2020 observations.

This modeling choice smooths the initial pandemic disruption for subsequent analysis. It does not reconstruct what quits rates would have been without the pandemic, and its effect on forecast accuracy has not been evaluated against models using the original observations.

The full-series chart uses original BLS observations. A separate chart compares the original and interpolated series. Subsequent seasonality, autocorrelation, decomposition, and forecasting analyses use the interpolated series.

Observations outside the February through October 2020 window remain unchanged.

## Analytical Framework

1. **Full series:** Historical quits rates with a linear trend overlay.
2. **Interpolation comparison:** Original observations alongside the interpolated series.
3. **Seasonality:** Calendar-month averages, annual overlays, and an STL estimated seasonal component.
4. **Autocorrelation:** ACF through 36 monthly lags, with approximate 95% white-noise reference bounds.
5. **STL decomposition:** Interpolated input, estimated trend, estimated seasonal component, and residual.
6. **Model validation:** Forecasts for 2025 compared using mean absolute error.
7. **2026 forecast:** A model fitted through December 2025, compared with actual observations through September 2026.

## Forecasting Approach

The notebook uses January 2016 through December 2024 as the training period, containing 108 observations. January through December 2025 provides 12 validation observations.

The training series includes the 2020 interpolation.

Four approaches are evaluated:

- **ETS:** Additive trend with no seasonal component.
- **ARIMA:** Five nonseasonal specifications compared using validation MAE.
- **Naive:** The final training observation repeated across the forecast horizon.
- **OLS + month effects:** Linear regression with a time trend and 11 month indicators, using December as the reference month.

### ARIMA Candidate Results

| ARIMA order | Validation MAE (ppts) | Training AIC |
| --- | ---: | ---: |
| (0,1,1) | 0.637 | 32.775 |
| (1,1,1) | 0.647 | 34.288 |
| (2,1,1) | 0.648 | 35.766 |
| (1,1,0) | 0.705 | 37.186 |
| (1,1,2) | 0.725 | 32.099 |

The selection criterion is validation MAE, not AIC. ARIMA(0,1,1) is selected in the saved results.

### Model Comparison

| Model | 2025 validation MAE |
| --- | ---: |
| ETS | 1.037 ppts |
| ARIMA(0,1,1) | 0.637 ppts |
| Naive | 0.725 ppts |
| OLS + month effects | 1.045 ppts |

ARIMA(0,1,1) has the lowest validation MAE. Its error is approximately 12.1% lower than the Naive benchmark, based on the rounded results.

Because 2025 is used to select the ARIMA specification, these results describe model-selection performance rather than an independent final test. Several ARIMA candidates perform similarly, and the ranking may change in another evaluation period.

### 2026 Forecast

The notebook refits the selected ARIMA order on the interpolated series through December 2025 and produces forecasts for January through December 2026 with 80% and 95% prediction intervals.

Observed 2026 values are excluded from fitting. The saved ARIMA(0,1,1) point forecast is flat, reflecting the specification without a drift term.

The forecast uses the current historical data download, which may contain revisions. It does not necessarily reproduce the forecast that would have been available in December 2025.

## Key Findings

- September 2026 recorded a quits rate of **5.0%**, up from **3.6% in August**, an increase of **1.4 percentage points**.
- September’s rebound interrupts the recent decline, but one observation does not establish a sustained reversal.
- Monthly averages range from approximately **4.63% to 4.85%**, a spread of about **0.22 percentage points**.
- January through September averages include 2026 observations, while October through December averages end in 2025. Unequal coverage and interpolation can influence these comparisons.
- The already seasonally adjusted series does not show a clear, stable remaining annual pattern. Larger recent STL seasonal fluctuations caution against describing remaining seasonality as uniformly weak.
- The ACF shows substantial persistence. Because it uses the interpolated level series, it can reflect longer-term movements and smoothing as well as serial dependence.
- The estimated STL trend remains downward at the end of the sample. Much of September’s rebound appears in the residual component.
- ARIMA(0,1,1) produces the lowest 2025 validation MAE among the evaluated models, but none captures the timing and magnitude of that year’s swings.
- The flat 2026 forecast does not reproduce the decline through August or September’s rebound. Prediction interval coverage alone does not establish accurate forecasting.

## Tools and Dependencies

- **Data handling:** pandas, NumPy
- **Visualization and styling:** Plotly, Matplotlib, Seaborn
- **Time-series analysis:** statsmodels
- **Regression and evaluation:** scikit-learn
- **API access:** requests
- **Certificate bundle:** certifi
- **Environment:** Jupyter Notebook

The notebook also imports Python’s built-in `json` module, which does not require installation.

## Reproducing the Analysis

1. Clone or download the repository and open `walk_in_september.ipynb` in Jupyter Notebook.

2. Install the required packages in the notebook’s Python environment:

   ```bash
   pip install pandas numpy matplotlib seaborn plotly statsmodels scikit-learn requests certifi
   ```

3. Obtain a BLS API key and enter it in the first code cell:

   ```python
   API_KEY = "Your Key Here"
   ```

4. Restart the kernel and run all cells in order.

5. Save the notebook with its outputs. Restore the API-key placeholder before sharing or committing the file.

The saved notebook explicitly uses `verify=certifi.where()` in the API request to select a certificate bundle while keeping TLS verification enabled.

The analysis cutoff and expected date range both end at September 2026. Updating to a later month requires changing both settings and reviewing the narrative and chart labels.

## Interpretation and Limitations

- BLS revisions can change historical values and results when the notebook is rerun.
- Interpolation replaces nine observed values with an assumed path and can influence subsequent analysis.
- Monthly averages alone do not demonstrate a recurring seasonal pattern.
- ACF and STL results are descriptive tools, not independent evidence of a particular forecasting model’s superiority.
- Model selection uses a single validation year.
- The notebook plots 2026 observations against the forecast but does not calculate a separate 2026 MAE or interval-coverage summary.
- Forecast intervals reflect the fitted model and its assumptions.

## Future Updates

Future editions can extend the comparison as additional 2026 observations become available.

The training cutoff remains December 2025, but rerunning the notebook repeats model selection and fitting using the latest historical data. Preserve the saved forecast results if the goal is to evaluate an unchanged forecast across editions. Revised forecasts should be identified separately.

**Jason Staats**
