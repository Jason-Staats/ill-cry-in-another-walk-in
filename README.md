# I’ll Cry in Another Walk-In
### A Time Series Analysis of Quit Rates in Accommodation and Food Services

## Overview

The accommodation and food services sector is widely associated with elevated employee turnover. This project examines monthly, seasonally adjusted quit rates in the sector from January 2016 through August 2026 using data from the U.S. Bureau of Labor Statistics Job Openings and Labor Turnover Survey.

The central question is how quit rates have changed since the pandemic-era surge in voluntary separations and how recent observations compare with pre-pandemic levels. The analysis also examines persistence, remaining seasonality after BLS adjustment, and how well several forecasting approaches predict subsequent quit rates.

This analysis serves as a companion to Up or On the Rocks?, which examines employment levels in the same sector using BLS Current Employment Statistics data.

## Data Source

- **Series:** JTS720000000000000QUR -- Quits Rate, Accommodation and Food Services (NAICS 72)
- **Provider:** U.S. Bureau of Labor Statistics, Job Openings and Labor Turnover Survey (JOLTS)
- **Period:** January 2016 – August 2026
- **Frequency:** Monthly
- **Units:** Quits rate (percentage of employment)
- **Access:** BLS Public Data API v2

The JOLTS quits rate measures voluntary separations during the month as a percentage of employment. A worker who chooses to leave is counted as a quit, while layoffs and other employer-initiated separations are not. The series measures voluntary departures, which can offer insight into workers' willingness to leave their jobs without directly measuring their reasons for doing so.

BLS applies seasonal adjustment before the data is retrieved. No additional seasonal adjustment is applied in this notebook.

JOLTS estimates are survey-based and subject to revision as additional responses are received and annual updates are applied.

## Methodology

**COVID-19 Treatment:** Quit rates fell sharply during the initial pandemic disruption in early 2020. Because the subsequent analysis examines longer-term trends, remaining seasonality after BLS adjustment, and persistence, this abrupt movement could influence the patterns identified.

To reduce that influence, quit rate values from February through October 2020 were replaced using time-based linear interpolation between the January and November 2020 observations. This creates a smooth transition across those nine months.

This is a modeling choice that replaces actual observed variation with an assumed path through the disruption. It does not reconstruct what quit rates would have been without the pandemic, and its effect on forecast accuracy has not been evaluated against a model using the original observations.

The original and interpolated series are both shown in the notebook for transparency. All subsequent analysis uses the interpolated series, with observations outside the February through October 2020 window unchanged.

**Analytical Framework:** The notebook follows a structured time series workflow:

1. Full series visualization with linear trend overlay
2. Analysis of remaining seasonality after BLS adjustment using monthly averages, year-over-year overlays, and STL
3. Autocorrelation analysis
4. STL decomposition into trend, seasonal, and residual components
5. Model comparison and selection using January through December 2025 as a holdout validation period
6. January through December 2026 forecasting using data through December 2025, with predictions compared against observed values through August 2026

**Forecasting:** January through December 2025 was held back as a validation set. Four forecasting approaches were trained on January 2016 through December 2024 and used to predict the following 12 months. The OLS model includes a linear time trend and month indicators.

Performance was evaluated using mean absolute error (MAE), measured in percentage points. Several nonseasonal ARIMA specifications were compared, with ARIMA(0,1,1) producing the lowest validation error.

| Model | Validation MAE |
|---|---|
| ETS | 1.037 ppts |
| ARIMA(0,1,1) | 0.637 ppts |
| Naive | 0.725 ppts |
| OLS | 1.045 ppts |

Because the 2025 results were used to select the model, these scores represent model-selection performance rather than an independent final test. The ranking reflects performance over this particular year and does not establish which model will perform best in future periods.

ARIMA(0,1,1) produced the lowest validation error and was selected for the 2026 forecast. Several other ARIMA specifications produced similar validation errors, while the simple Naive forecast also proved competitive.

The selected model was retrained on the interpolated series through December 2025 to generate monthly forecasts for January through December 2026 with 80% and 95% prediction intervals. Its point forecast is flat at approximately 4.53%. Observed 2026 values were not used to fit the model and are compared against this fixed forecast to assess its performance through August.

## Key Findings

- Quit rates remained relatively steady from 2016 through 2019, averaging around 4.5%.

- From mid-2021 through early 2022, quit rates remained elevated, fluctuating around 6% and staying well above the pre-pandemic range.

- Rates have broadly declined since the pandemic-era surge, with recent observations falling below the pre-pandemic range.

- From 2025 through August 2026, observed quit rates ranged from 3.3% to 5.5%, compared with 3.9% to 5.1% during 2016 through 2019. These descriptive comparisons alone cannot establish whether the underlying baseline has changed.

- Volatility increased considerably in 2025, with the quits rate moving across a range of more than two percentage points. Through August 2026, month-to-month swings have moderated, although rates have continued to decline.

- August 2026 recorded a quit rate of 3.5%, down 0.2 percentage points from July's 3.7%. August's reading is among the lowest observations in the full dataset.

- The BLS seasonally adjusted series shows limited evidence of a stable remaining annual pattern. The STL seasonal component becomes more variable near the end of the sample, so remaining seasonality should not be described as uniformly weak.

- The autocorrelation function shows substantial persistence, meaning periods of relatively high or low quit rates tend to carry forward into subsequent months. The level-series ACF does not establish a specific autoregressive structure on its own.

- ARIMA(0,1,1) produced the lowest 2025 validation MAE at 0.637 percentage points, outperforming ETS, OLS, and the Naive benchmark in that validation period. These results were used for model selection and do not establish future performance.

- The fixed 2026 ARIMA point forecast is approximately 4.53%. Actual quit rates were below it every month from February through August. August's rate was approximately 1.03 percentage points below the forecast.

- Both July and August fell below their respective 80% prediction intervals while remaining inside their 95% intervals. August's intervals were 3.69% to 5.37% and 3.24% to 5.82%, respectively.

- The pandemic-era increase in quit rates has reversed, but the fixed forecast has not captured the recent decline. Remaining within the wider prediction interval does not establish that the forecast is tracking that decline well.

## Tools and Libraries

- **Python** -- pandas, numpy, matplotlib, statsmodels, scikit-learn, plotly, seaborn, requests
- **Environment** -- Jupyter Notebook
- **Data Access** -- BLS Public Data API v2

## Running the Notebook

1. Clone the repository and open walk_in_august.ipynb in Jupyter Notebook.
2. Install dependencies:
   `pip install pandas matplotlib numpy statsmodels scikit-learn plotly seaborn requests`
3. Register for a free BLS API key at [https://data.bls.gov/registrationEngine/](https://data.bls.gov/registrationEngine/)
4. Set API_KEY in the first code cell to your own key. Remove the key before sharing or committing the notebook.
5. Restart the kernel and run all cells in order, then save the notebook.

This edition limits observations to January 2016 through August 2026 and checks that every month in that period is present. Updating to a later edition requires changing both the cutoff and the expected date range, then reviewing the narratives and chart labels.

Rerunning the notebook retrieves the latest available BLS revisions, so historical values and model results may change even with the August cutoff.

## Planned Updates

This project is designed as a living analysis. Future editions will compare newly released BLS JOLTS observations with the fixed January through December 2026 ARIMA forecast, which was fitted using data through December 2025. Any model refitting or specification changes will be documented separately from that forecast evaluation.

Key questions to track throughout 2026 include:

- Do quit rates remain below the pre-pandemic range or move back toward it?
- Does the gap between the point forecast and actual observations widen or narrow?
- Does the lower quit-rate environment persist, or does the volatility observed in 2025 return?
- How do quit-rate trends compare with the employment trajectory documented in the companion analysis?

Future editions will also identify relevant BLS revisions and update the analysis period, narratives, and charts.
  
## Author

Jason Staats
