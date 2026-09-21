# Mobile Network Traffic Forecasting

This project compares sequential models for one-step-ahead mobile network traffic forecasting using the Telecom Italia Milan dataset. The raw event-level records are streamed from [Harvard Dataverse](https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/EGZHFV), aggregated by spatial square and 10-minute interval, and stored as Snappy-compressed Parquet files to reduce memory usage.

The notebook analyzes traffic patterns across Milan's $100 \times 100$ spatial grid and evaluates four forecasting approaches on the highest-traffic areas:

- Fourier SARIMAX
- Random Forest
- Optimized LSTM
- Optimized GRU

Models are compared using MAE, MAPE, RMSE, $R^2$, training time, inference time, and memory usage. The analysis includes spatial and temporal exploratory analysis, autocorrelation, seasonal decomposition, stationarity tests, chronological validation, residual diagnostics, and final test-set evaluation.

## Project Files

- [`timeseries_network_traffic_forcasting.ipynb`](timeseries_network_traffic_forcasting.ipynb): complete data-ingestion, analysis, training, and evaluation workflow.
- [`requirements.txt`](requirements.txt): Python dependencies used by the notebook.

## Requirements

- Google Colab or another environment with Python 3.10+.
- A Google account with Google Drive access when running in Colab.
- Sufficient storage for the downloaded raw files and generated Parquet files.
- Optional GPU acceleration for the LSTM and GRU experiments.

The notebook uses Google Colab-specific imports such as `google.colab.drive` and `google.colab.userdata`. It is therefore designed to run in Google Colab without local-environment changes.

## Setup

### 1. Open the notebook

Upload or open [`timeseries_network_traffic_forcasting.ipynb`](timeseries_network_traffic_forcasting.ipynb) in Google Colab.

### 2. Install dependencies

Run this in a Colab cell if the runtime does not already contain the required packages:

```python
%pip install -r requirements.txt
```

### 3. Configure the Dataverse API token

The notebook reads the API token from Colab Secrets using the key `MY_TOKEN`:

1. Open the **Secrets** panel in Google Colab.
2. Add a secret named `MY_TOKEN`.
3. Paste your Harvard Dataverse API token as the secret value.
4. Enable notebook access to the secret when prompted.

### 4. Enable Google Drive access

The configuration cell mounts Google Drive at `/content/drive` and creates these directories:

```text
/content/drive/MyDrive/milan_traffic_project
/content/temp_raw_data
```

## Running the Workflow

Run the notebook cells from top to bottom. The main stages are:

1. Import libraries and configure reproducibility settings.
2. Mount Google Drive and prepare local and persistent storage.
3. Download and aggregate Milan daily records from Harvard Dataverse.
4. Rebuild traffic totals and identify the highest-traffic spatial squares.
5. Build target time series and perform exploratory data analysis.
6. Create chronological training, validation, and test splits.
7. Engineer autoregressive lag and temporal features.
8. Train and evaluate SARIMAX, Random Forest, LSTM, and GRU models.
9. Tune the recurrent models using validation data.
10. Run the final out-of-sample evaluation for December 16–22, 2013.

The first ingestion run may take 15–20 minutes depending on network speed. Once Parquet files exist in Google Drive, rerunning the ingestion section uses the cached files where possible.

## Data and Evaluation

The project uses the Milan mobile-network traffic data published by Telecom Italia through Harvard Dataverse. Records cover approximately November 2013 through January 2014 at 10-minute intervals.

The chronological split is:

- Training: through December 8, 2013
- Validation: December 9–15, 2013
- Test: December 16–22, 2013

## References

- Barlacchi et al. (2015), [A multi-source dataset of urban life in the city of Milan and the Province of Trentino](https://doi.org/10.1038/sdata.2015.55).
- Telecom Italia (2015), [Telecommunications - SMS, Call, Internet - MI](https://doi.org/10.7910/DVN/EGZHFV).
