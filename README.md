# vLLM Metrics Collection and Dashboarding

This repository contains configuration files for monitoring a vLLM instance using Telegraf, InfluxDB, and Grafana.

## Overview

This setup enables comprehensive monitoring of vLLM performance metrics by:
- Collecting metrics from vLLM's Prometheus endpoint
- Sending metrics to InfluxDB for storage and querying
- Filtering unnecessary metrics to reduce overhead
- Providing visualization capabilities through Grafana

```
vLLM Service (Prometheus-compatible Endpoint) → Telegraf → InfluxDB → Grafana Dashboard
```

### Dashboard Preview

Example screenshots of the Grafana dashboard are shown below, each displaying multiple panels:

> **TODO**: add screenshots..

![Dashboard Overview 1](screenshots/dashboard-overview-1.png)
*Main dashboard with throughput, request decode time, and token statistics.*

![Dashboard Overview 2](screenshots/dashboard-overview-2.png)

*Panels for active requests, HTTP response codes, Python GC collections, and GPU cache usage.*


## Data Collection Setup (Telegraf)

### Configuration File
The main configuration file `telegraf.conf` includes:

1. **Agent Settings**: Configures collection intervals, batch sizes, and buffer limits
2. **InfluxDB Output**: Sends metrics to InfluxDB 2.0 with authentication
3. **Metric Filtering**: Drops specific metrics to reduce data volume
4. **Prometheus Input**: Scrapes metrics from vLLM's Prometheus endpoint

### Required Environment Variables
Before deploying, update the following placeholders in the configuration:
- `[YOUR-INFLUXDB-URL.COM]` - Your InfluxDB instance URL
- `[YOUR_INFLUXDB_TOKEN_HERE]` - Authentication token for InfluxDB
- `[YOUR_ORGANIZATION_NAME]` - InfluxDB organization name
- `[YOUR_BUCKET_NAME]` - Target bucket for metrics
- `[YOUR-VLLM-ENDPOINT.com]` - Hostname of your vLLM service
- `[PORT]` - Port number where vLLM exposes metrics

### Deployment Instructions
1. Install Telegraf on your monitoring server
2. Place the `telegraf.conf` file in `/etc/telegraf/`
3. Update the configuration with your actual values
4. Start the Telegraf service

Or, refer to the [Telegraf Deployment Strategies with Docker Compose](https://www.influxdata.com/blog/telegraf-deployment-strategies-docker-compose/) guide.


## Grafana Dashboard

A Grafana dashboard configuration (`grafana-dashboard.json`) is included to visualize the collected metrics. This dashboard provides insights into:
- vLLM performance metrics
- Resource utilization
- Request processing statistics

### Dashboard Overview

The dashboard features the following panels:

* **Throughput (tokens per second)**: Displays the average, minimum, and maximum number of tokens processed per second for both prompt and generation.
* **Request Decode Time (seconds)**: Shows the 50th, 90th, and 99th percentile decode times for requests, providing insight into request latency.
* **Prompt Token**: Visualizes the total and new prompt tokens processed.
* **Generation Token Counts**: Visualizes the total and new generation tokens processed.
* **Active Requests**: Presents the count of currently running and waiting requests.
* **HTTP Response Code**: Displays the number of successful (2xx) and failed (non-2xx) HTTP requests, indicating service health.
* **Python GC Collections**: Shows the frequency of Python garbage collection cycles, which can be indicative of memory management issues.
* **GPU Cache**: Monitors the GPU cache usage percentage.

### Installation

1. **Import the Dashboard**: In your Grafana instance, navigate to "Create" → "Import".
2. **Paste the JSON**: Copy the contents of the `grafana.json` file and paste it into the import field.
3. **Select Data Source**: Choose the InfluxDB data source you have configured that connects to your InfluxDB instance.
4. **Configure Bucket Name**: This dashboard is configured to query data from the InfluxDB bucket named `vllm-metrics`. Ensure your InfluxDB connection is configured to use this bucket, or modify the queries within the `grafana-dashboard.json` file to reflect your bucket name.
5. **Configure Placeholders**: The dashboard uses placeholders for dynamic values. You'll need to replace these in the queries to match your vLLM deployment:
  * `[METRICS_ENDPOINT]`: Replace this with the full URL of your vLLM metrics endpoint (e.g., `http://localhost:8000/metrics`).
  * `[MODEL_NAME]`: Replace this with the name of the model being served by your vLLM instance (e.g., `gemma3:27b`).
  * `[DATASOURCE_UID]`: Replace this with the UID of your InfluxDB data source in Grafana. This placeholder was added to support dynamic data source configuration.
6. **Complete Import**: Click "Import" to create the dashboard.

**Troubleshooting: Data Source UID** - If the dashboard doesn't display data after import, double-check that the UID of your InfluxDB data source in Grafana matches the UID specified in the `grafana-dashboard.json` file (it looks like `40e65bd7-9940-4181-bb68-b54f0f5fe4ae`). You can update the UID within the `datasource` section of the `grafana-dashboard.json` file if necessary.

## Prerequisites

- Telegraf 1.30+
- InfluxDB 2.0+
- Grafana 8.0+
- vLLM service with Prometheus metrics enabled

## Troubleshooting

Common issues and solutions:
1. **No metrics received**: Check vLLM Prometheus endpoint accessibility
2. **Connection failures**: Verify InfluxDB URL and authentication token
3. **Filtering issues**: Review filter rules in the configuration

## License

This project is licensed under the MIT License - see the LICENSE file for details.
