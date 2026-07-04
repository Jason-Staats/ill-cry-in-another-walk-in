# I’ll Cry in Another Walk-In
### A Time Series Analysis of Quit Rates in Accommodation and Food Services

## Overview
The accommodation and food services sector is widely associated with elevated employee turnover. This project examines monthly quit rates in the sector from January 2016 through May 2026 using data from the U.S. Bureau of Labor Statistics Job Openings and Labor Turnover Survey. The central question is whether the post-pandemic surge in voluntary separations has fully unwound, or whether elevated turnover remains a structural feature of the industry.

This analysis serves as a companion to *Up or On the Rocks?*, which examines
employment levels in the same sector over the same period using BLS Current
Employment Statistics data.

## Data Source
- **Series:** JTS720000000000000QUR -- Quits Rate, Accommodation and Food Services (NAICS 72)
- **Provider:** U.S. Bureau of Labor Statistics, Job Openings and Labor Turnover Survey (JOLTS)
- **Period:** January 2016 – May 2026
- **Frequency:** Monthly
- **Units:** Quits rate (percentage of employment)
- **Access:** BLS Public Data API v2

A note on the data: the JOLTS quits rate measures voluntary separations as a
percentage of total employment in the reference period. It captures worker-initiated
exits and is distinct from layoffs, discharges, or other employer-initiated
separations. The series reflects the relative ease with which workers feel they can
leave a job, making it a useful proxy for labor market confidence and retention
pressure in the sector.

## Methodology
**COVID-19 Treatment:** The pandemic caused a sharp contraction in quit activity
beginning in early 2020, representing an external shock driven by uncertainty and
reduced mobility rather than a shift in underlying labor market dynamics. Quit rate
values from February 2020 through October 2020 were replaced with linearly
interpolated estimates during decomposition and forecasting stages to preserve the
long-term trend structure. The original and interpolated series are both shown in
the notebook for full transparency.

**Analytical Framework:** The notebook follows a structured time series workflow:
1. Full series visualization with linear trend overlay
2. Seasonality analysis including monthly averages, year-over-year overlay, and STL seasonal component
3. Autocorrelation analysis
4. STL decomposition into trend, seasonal, and residual components
5. Multi-model forecasting with holdout validation

**Forecasting:** Four models were trained on January 2016 through December 2024 and
validated against actual January through December 2025 quit rates, representing one
complete calendar year. Model performance was assessed using Mean Absolute Error in
percentage points.

| Model | Validation MAE |
|---|---|
| ETS | 1.037 ppts |
| ARIMA | 0.647 ppts |
| Naive | 0.725 ppts |
| OLS | 1.045 ppts |

ARIMA(1,1,1) produced the lowest error and was selected to generate the 2026 forward
forecast. A seasonal ARIMA specification was not used, as autocorrelation analysis
showed gradual autocorrelation decay rather than the discrete seasonal spikes that would justify
a seasonal order.

## Key Findings
- Quit rates in the sector averaged approximately 4.5% from 2016 through early 2020,
  reflecting the sector's historically elevated baseline turnover.
- The pandemic sharply suppressed quit activity in early 2020, coinciding with widespread
  labor market uncertainty and reduced worker mobility.
- Quit rates rose sharply between mid-2021 and early 2022, reaching a sustained peak of roughly
  6%, well above pre-pandemic norms.
- Rates declined gradually from 2022 through 2024, approaching pre-pandemic levels by late 2024.
- 2025 introduced unexpected volatility, with quit rates briefly spiking to 5.5% in June before
  falling back sharply, behavior not captured by models trained on pre-2025 data.
- The series shows negligible seasonal structure, in contrast to the strong summer employment
  peaks observed in the same sector.
- January through May 2026 quit rates ranged from 4.0% to 4.7%, all falling within the ARIMA 80%
  confidence interval. The model's persistence forecast captures the general level but has
  consistently understated month-to-month variability. The forecast remains appropriately
  calibrated to the actual volatility observed in recent years.
  
## Tools and Libraries
- **Python** -- pandas, numpy, statsmodels, scikit-learn, plotly, seaborn
- **Environment** -- Jupyter Notebook
- **Data Access** -- BLS Public Data API v2

## Running the Notebook
1. Clone the repository
2. Install dependencies: `pip install pandas numpy statsmodels scikit-learn plotly seaborn requests`
3. Register for a free BLS API key at [https://data.bls.gov/registrationEngine/](https://data.bls.gov/registrationEngine/)
4. Replace the `API_KEY` value in the first code cell with your key
5. Run all cells in order

## Planned Updates
This project is designed as a living analysis. The ARIMA model projects quit rates
through December 2026, and actual BLS JOLTS data will be incorporated monthly as
releases become available. JOLTS estimates are subject to revision as additional
survey data becomes available. Key questions to track throughout 2026:

- Does the mid-2025 spike represent a temporary disruption or the beginning of a
  renewed upward trend?
- Does the forecast gap widen or narrow as actual monthly data accumulates?
- How do quit rate trends align with the employment level trajectory documented in
  the companion analysis?

## Author
Jason Staats
