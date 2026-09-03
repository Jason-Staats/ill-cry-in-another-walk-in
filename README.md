# I’ll Cry in Another Walk-In
### A Time Series Analysis of Quit Rates in Accommodation and Food Services

## Overview

The accommodation and food services sector is widely associated with elevated employee turnover. This project examines monthly quit rates in the sector from January 2016 through July 2026 using data from the U.S. Bureau of Labor Statistics Job Openings and Labor Turnover Survey.

The central question is whether the post-pandemic surge in voluntary separations has fully subsided, or whether elevated turnover remains a structural feature of the industry. The analysis also examines persistence, seasonality, and how well several forecasting approaches predict unseen quit-rate data.

This analysis serves as a companion to *Up or On the Rocks?*, which examines employment levels in the same sector using BLS Current Employment Statistics data.

## Data Source

- **Series:** JTS720000000000000QUR -- Quits Rate, Accommodation and Food Services (NAICS 72)
- **Provider:** U.S. Bureau of Labor Statistics, Job Openings and Labor Turnover Survey (JOLTS)
- **Period:** January 2016 – July 2026
- **Frequency:** Monthly
- **Units:** Quits rate (percentage of employment)
- **Access:** BLS Public Data API v2

The JOLTS quits rate measures voluntary separations as a percentage of total employment in the reference period. A worker who chooses to leave is counted as a quit, while layoffs and other employer-initiated separations are not. As a result, the series provides a view of how willing workers are to leave their jobs rather than simply how many people are employed.

JOLTS estimates are survey-based and subject to revision as additional responses are received.

## Methodology

**COVID-19 Treatment:** Quit rates in early 2020 deviated sharply from the levels and patterns observed throughout the rest of the series. Because the subsequent analysis is intended to identify underlying trend, seasonality, and persistence, this unusual period could disproportionately influence those patterns.

Quit rate values from February through October 2020 were therefore replaced using linear interpolation between the surrounding observations. This creates a smooth transition through the pandemic disruption rather than allowing those nine months to drive the characteristics identified by the models. The original and interpolated series are both shown in the notebook for transparency.

**Analytical Framework:** The notebook follows a structured time series workflow:

1. Full series visualization with linear trend overlay
2. Seasonality analysis using monthly averages, year-over-year overlays, and STL
3. Autocorrelation analysis
4. STL decomposition into trend, seasonal, and residual components
5. Holdout validation across multiple forecasting approaches
6. Forward forecasting through December 2026

**Forecasting:** The most recent full year of available historical data, January through December 2025, was held back as a validation set. Four forecasting approaches were trained on January 2016 through December 2024 and asked to predict the 12 months they had never seen.

Performance was evaluated using Mean Absolute Error in percentage points. Several nonseasonal ARIMA specifications were also compared within the ARIMA approach.

| Model | Validation MAE |
|---|---|
| ETS | 1.037 ppts |
| ARIMA(0,1,1) | 0.637 ppts |
| Naive | 0.725 ppts |
| OLS | 1.045 ppts |

ARIMA(0,1,1) produced the lowest validation error and was selected for the final 2026 forecast. The closely related ARIMA specifications produced similar results, while the simple Naive forecast also proved competitive. This is consistent with the substantial persistence and limited annual seasonality observed earlier in the analysis.

The selected model was retrained on the full series through December 2025 and used to generate monthly forecasts through December 2026 with 80% and 95% forecast intervals.

## Key Findings

- Quit rates remained relatively steady between 2016 and early 2020, averaging around 4.5%.

- From mid-2021 through early 2022, quit rates climbed to a sustained peak of roughly 6%, well above anything observed during the pre-pandemic period.

- Rates have trended downward since the pandemic-era peak, with observations returning to ranges comparable with the pre-pandemic period.

- From 2025 through July 2026, observed quit rates ranged from 3.3% to 5.5%, compared with a pre-pandemic range of 3.9% to 5.1%. The substantial overlap provides little evidence so far of a fundamentally different structural baseline.

- Volatility increased considerably in 2025, with the quits rate moving across a range of more than two percentage points. Through July 2026, those swings have moderated.

- July 2026 recorded a quit rate of 3.5%, one of the lowest observations in the full dataset.

- The series shows limited evidence of a strong or consistent annual seasonal pattern. This contrasts with accommodation and food services employment, which exhibits much clearer seasonal movement.

- The autocorrelation function shows substantial persistence, meaning periods of relatively high or low quit rates tend to carry forward into subsequent months. The level-series ACF does not, however, establish a specific autoregressive structure on its own.

- ARIMA(0,1,1) produced the lowest 2025 validation MAE at 0.637 percentage points, outperforming ETS, OLS, and the Naive benchmark.

- The 2026 ARIMA point forecast remains relatively flat. Actual quit rates through July have generally tracked below the point forecast. The July observation falls outside the narrower 80% forecast interval but remains within the 95% interval.

- Taken together, the decline from the pandemic-era peak and return toward earlier ranges suggest that much of the extraordinary increase in quit activity has subsided, although recent month-to-month variability remains difficult to forecast precisely.

## Tools and Libraries

- **Python** -- pandas, numpy, statsmodels, scikit-learn, plotly, seaborn
- **Environment** -- Jupyter Notebook
- **Data Access** -- BLS Public Data API v2

## Running the Notebook

1. Clone the repository
2. Install dependencies:
   `pip install pandas numpy statsmodels scikit-learn plotly seaborn requests`
3. Register for a free BLS API key at [https://data.bls.gov/registrationEngine/](https://data.bls.gov/registrationEngine/)
4. Replace the `API_KEY` value in the first code cell with your key
5. Run all cells in order

## Planned Updates

This project is designed as a living analysis. The ARIMA model projects quit rates through December 2026, and actual BLS JOLTS observations will be incorporated as monthly releases become available.

Key questions to track throughout 2026 include:

- Do quit rates continue moving toward or below the pre-pandemic range?
- Does the gap between the point forecast and actual observations widen or narrow as additional data becomes available?
- Does the lower quit-rate environment persist, or does the volatility observed in 2025 return?
- How do quit-rate trends compare with the employment trajectory documented in the companion analysis?

## Author

Jason Staats
