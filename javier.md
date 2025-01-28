# Code Description: Top 10 Stocks by ROC/STDDEV Ratio and QTD Sector Returns

## Overview
This script performs the following key tasks:
1. **Extract and Analyze Historical Data**:
   - Reads ETF and stock historical data from an S3 bucket.
   - Filters data based on ETF and sector criteria.
   - Calculates financial metrics like ROC (Rate of Change), STDDEV (Standard Deviation), and their ratio (ROC/STDDEV).
   - Ranks the top 10 stocks by the ROC/STDDEV ratio for each ETF and date.

2. **Calculate QTD Returns**:
   - Computes Quarter-To-Date (QTD) returns for a list of ETFs over rolling 15-business-day intervals.
   - Identifies the top 3 performing ETFs for each interval.

3. **Select Top Stocks**:
   - For each top-performing ETF, retrieves the top 4 stocks based on the ROC/STDDEV ratio.
   - Consolidates these stocks into a list of 12 stocks per interval.

4. **Save Results**:
   - Exports the ranked data to Excel and CSV files.
   - Generates a JSON file containing the selected stocks for each date.

## Key Functions and Logic

### AWS S3 Integration
- **`download_and_read_csv(bucket, file_name)`**:
  Downloads a CSV file from an S3 bucket and loads it into a Pandas DataFrame.

- **`upload_file_to_s3(bucket, file_name, data, content_type)`**:
  Uploads files to the S3 bucket, handling file formats like Excel.

---

### Stock Data Analysis
- **Filtering and Preparation**:
  Filters stocks by specific ETFs and categories, ensures valid data types, and drops missing values.

- **`calculate_roc(df, period=22)`**:
  Calculates the Rate of Change (ROC) over a given period (default: 22 days).

- **`calculate_stddev(df, period=22)`**:
  Computes the rolling standard deviation of returns over a given period.

- **`calculate_roc_stddev_ratio(df)`**:
  Combines ROC and STDDEV to compute their ratio, which serves as a metric for ranking stocks.

- **`rank_top_10_by_etf(df, etf_column, metric)`**:
  Ranks the top 10 stocks by the given metric (ROC/STDDEV ratio) for each ETF and date.

- **`pivot_ranked_data_by_etf(df, etf)`**:
  Pivots ranked stock data by ETF, creating a tabular format with dates as columns.

---

### QTD Returns Calculation
- **`fetch_historical_data(etf, api_token, start_date, end_date)`**:
  Fetches historical price data for a given ETF using the EOD Historical Data API.

- **`calculate_qtd_for_etf(etf, api_token, start_of_quarter, current_date)`**:
  Computes QTD returns by comparing prices at the start and end of a quarter.

---

### Sector and Stock Selection
- **`rank_sectors_by_performance(df)`**:
  Ranks ETFs by their QTD returns for each date.

- **Stock Selection Loop**:
  Iterates through top-performing ETFs, extracts the top 4 stocks for each, and consolidates them into a final list.

---

### File Handling and Output
- Saves results to local Excel and CSV files.
- Exports consolidated stock lists to a JSON file for easy reference and further use.

---

## Purpose
This script provides:
1. A ranked list of top-performing stocks by financial metrics for each ETF.
2. Rolling QTD returns for sector ETFs, allowing identification of outperforming sectors.
3. A consolidated list of top stocks from high-performing ETFs for each period, enabling focused investment decisions.

---

## Example Outputs
1. **Excel File**: `top_10_stocks_rankings_by_etf.xlsx` containing rankings of top 10 stocks by ROC/STDDEV ratio for each ETF.
2. **CSV File**: `top_9_stocks_by_date.csv` with the top 12 stocks for each date (3 ETFs × 4 stocks).
3. **JSON File**: `id_kamila_<date>.json` with the top stocks listed by date for quick access.

---

## Key Details
- **Stock Metric**: ROC/STDDEV ratio provides insight into risk-adjusted returns.
- **Frequency**: Runs every 15 business days to update rankings and QTD returns.
- **Dynamic Adaptation**: Automatically skips invalid or missing data to ensure robustness.
