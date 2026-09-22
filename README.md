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

## Setup

### 1. Open the notebook

Upload or open [`timeseries_network_traffic_forcasting.ipynb`](timeseries_network_traffic_forcasting.ipynb) in Google Colab.

### 2. Install dependencies

Run this in a Colab cell if the runtime does not already contain the required packages:

```python
pip install -r requirements.txt
```

### 3. Configure the Dataverse API token

You can provide your token directly or let the notebook read the API token from Colab Secrets using the key `MY_TOKEN`:

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

## **Test Set Evaluation Summary**
### **Square 5161 (Highest Traffic Hotspot)**

| Model Name | MAE | MAPE (%) | RMSE | $R^2$ Score | Fit Time | Infer Time | RAM Usage |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Fourier SARIMAX** | 86.29 | 10.52% | 147.73 | 0.9882 | **1.68s** | **0.050s** | 1534.60 MB |
| **Random Forest** | 95.91 | **9.27%** | 142.04 | 0.9891 | 2.49s | 0.048s | **1509.76 MB** |
| **Optimized LSTM** | 0.01 | 15.41% | 0.02 | 0.9910 | 20.75s | 0.294s | 11721.0 MB |
| **Optimized GRU** | **0.01** | 15.88%| **0.02** | **0.9920** | 23.25s | 0.296s | 1711.15 MB |

---
### **Square 5059 (Second Highest Traffic)**

| Model Name | MAE | MAPE (%) | RMSE | $R^2$ Score | Fit Time | Infer Time | RAM Usage |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Fourier SARIMAX** | 71.77 | **8.01%** | 114.81 | 0.9850 | **2.25s** | **0.016s** | 1547.18 MB |
| **Random Forest** | 81.31 | 8.09% | 113.69 | 0.9853 | 3.39s | 0.058s | **1529.29 MB** |
| **Optimized LSTM** | 0.02 | 15.99% | 0.02 | 0.9883 | 19.66s | 0.270s | 1744.76 MB |
| **Optimized GRU** | **0.02** | 14.98% | **0.02** | **0.9895** | 20.52s | 0.414s | 1732.43 MB |

---
### **Square 5259 (Third Highest Traffic)**

| Model Name | MAE | MAPE (%) | RMSE | $R^2$ Score | Fit Time | Infer Time | RAM Usage |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Fourier SARIMAX** | 75.23 | 10.19% | 104.40 | 0.9913 | **1.15s** | **0.015s** | 1548.55 MB |
| **Random Forest** | 69.27 | **8.14%** | 99.84 | 0.9921 | 3.66s | 0.046s | **1547.82 MB** |
| **Optimized LSTM** | 0.02 | 10.93% | 0.02| 0.9921 | 18.26s | 0.266s | 1752.33 MB |
| **Optimized GRU** | **0.02** | 10.77% | **0.02** | **0.9923**| 18.79s | 0.281s | 1739.38 MB |

---
## References

- Barlacchi et al. (2015), [A multi-source dataset of urban life in the city of Milan and the Province of Trentino](https://doi.org/10.1038/sdata.2015.55).
- Telecom Italia (2015), [Telecommunications - SMS, Call, Internet - MI](https://doi.org/10.7910/DVN/EGZHFV).
