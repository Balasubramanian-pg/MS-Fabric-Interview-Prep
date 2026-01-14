# How Do You Implement Anomaly Detection Functions In Kql

Canonical documentation for How Do You Implement Anomaly Detection Functions In Kql. This document defines concepts, terminology, and standard usage.

## Purpose
The implementation of anomaly detection in Kusto Query Language (KQL) addresses the need to identify statistically significant deviations within time-series data. In large-scale telemetry and logging environments, manual inspection of data points is unfeasible. Anomaly detection functions provide an automated, algorithmic approach to distinguish between expected fluctuations (such as seasonality and trends) and genuine outliers that may indicate system failures, security breaches, or performance degradation.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative regarding the logic and application of KQL's native time-series analysis capabilities.

## Scope
**In scope:**
*   **Time-series preparation:** The transformation of tabular data into a format suitable for analysis.
*   **Decomposition logic:** The theoretical framework of Seasonal-Trend decomposition using Loess (STL).
*   **Statistical scoring:** The methods used to quantify "outlierness" (e.g., Z-score, Tukey’s fences).
*   **Function selection:** Criteria for choosing between specific native KQL functions.

**Out of scope:**
*   External Machine Learning (ML) model integration (e.g., Python/R plugins).
*   Specific vendor-specific alerting UI configurations.
*   Data ingestion strategies.

## Definitions
| Term | Definition |
|------|------------|
| **Time-series** | A sequence of data points indexed in successive order, usually with equal intervals between points. |
| **Seasonality** | Periodic fluctuations in data that repeat at regular intervals (e.g., daily or weekly cycles). |
| **Trend** | The long-term increase or decrease in the data's mean value over time. |
| **Residual** | The remaining component of a time-series after the trend and seasonal components have been removed; also known as "noise." |
| **Decomposition** | The process of deconstructing a time-series into its trend, seasonal, and residual components. |
| **Z-score** | A statistical measurement that describes a value's relationship to the mean of a group of values, measured in terms of standard deviations. |

## Core Concepts
Anomaly detection in KQL is built upon the foundation of **Time-Series Analysis**. Unlike simple thresholding (e.g., `count > 100`), anomaly detection accounts for the historical context of the data.

### 1. Series Creation
Before detection can occur, discrete log entries must be aggregated into a continuous series using the `make-series` operator. This operator regularizes the data by filling missing bins, which is a prerequisite for mathematical decomposition.

### 2. The Decomposition Model
KQL primarily utilizes the **STL (Seasonal-Trend decomposition using Loess)** model. The logic follows the additive formula:
`Baseline = Trend + Seasonality`
`Actual Value = Baseline + Residual`

An anomaly is defined as a point where the **Residual** exceeds a calculated statistical threshold relative to the expected baseline.

### 3. Detection Algorithms
*   **`series_decompose_anomalies`**: Uses STL decomposition to identify anomalies by analyzing the residual component. It is best suited for data with clear seasonal patterns.
*   **`series_outliers`**: Uses a simpler statistical approach (typically Tukey's fences) to identify points that deviate significantly from the distribution of the series. It does not account for seasonality or trend.

## Standard Model
The standard implementation workflow for anomaly detection in KQL follows a three-stage pipeline:

1.  **Aggregation (`make-series`)**: Define the metric, the time range, and the step (bin) size. Missing values should be handled using the `default` parameter (usually `0` or `double(null)`).
2.  **Detection (`series_decompose_anomalies`)**: Apply the function to the generated series. This returns a series of scores where:
    *   `0` indicates a normal point.
    *   `1` or `-1` indicates a positive or negative anomaly.
3.  **Filtering and Visualization**: Filter the results to isolate the anomalies and use the `render` operator (e.g., `render timechart`) to visualize the actuals against the predicted baseline.

## Common Patterns

### Seasonal Detection
When monitoring business metrics (e.g., login counts), users implement `series_decompose_anomalies` with a specified `period`. If the period is unknown, KQL can auto-detect it, though explicit definition improves accuracy.

### Threshold Tuning
The `threshold` parameter in KQL functions acts as a sensitivity dial. A higher threshold (e.g., `3.0` or `4.0`) reduces false positives by requiring a more significant deviation from the mean to trigger an anomaly flag.

### Comparative Analysis
Implementing detection across multiple dimensions (e.g., per-region or per-service) by using the `by` clause in the `make-series` operator. This allows the algorithm to establish unique baselines for different data subsets.

## Anti-Patterns
*   **Detecting on Raw Logs**: Attempting to find anomalies without first using `make-series`. KQL anomaly functions require an array of equidistant numerical values.
*   **Ignoring Data Sparsity**: Applying detection to series with too many null/zero values. This leads to "flat-line" baselines where any minor activity is flagged as an anomaly.
*   **Short Lookback Windows**: Providing insufficient historical data (e.g., only 1 hour of data for a daily cycle). The algorithm requires at least two full periods to accurately identify seasonality.
*   **Over-reliance on Auto-detection**: Relying on the engine to guess the `period` for complex, multi-seasonal data, which often leads to inaccurate baselines.

## Edge Cases
*   **Step Changes**: A permanent shift in a metric's baseline (e.g., after a successful software deployment). The algorithm may flag every point following the shift as an anomaly until the trend component eventually adjusts.
*   **Clock Changes (DST)**: Daylight Savings Time shifts can introduce artificial anomalies in data with strict hourly seasonality.
*   **Zero-Inflation**: In metrics that are usually zero, a single occurrence (e.g., one error in a month) may be statistically flagged as a massive anomaly despite being operationally insignificant.

## Related Topics
*   **Time-Series Forecasting**: Using `series_decompose_forecast` to predict future values based on the same STL logic.
*   **Linear Regression**: Using `series_fit_line` for simple trend analysis without seasonality.
*   **Statistical Clustering**: Using `basket` or `autocluster` for non-time-series outlier detection.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |