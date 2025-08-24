# 📊 Metrics Types in Prometheus

Prometheus metrics are divided into **four core metric types**.  
Each type serves a specific purpose in monitoring and alerting.

---

## 1️⃣ Counter

- **Definition**:  
  A **monotonically increasing value** (only increases over time, or resets to zero on restart).  
  Best for **counting events**.

- **Use Cases**:
  - Number of requests served
  - Errors occurred
  - Tasks completed

- **Example**:
  ```promql
  http_requests_total{method="GET", status="200"}
Important: **To calculate the rate of change of a counter over time, use rate() or increase():  rate(http_requests_total[5m])**

## 2️⃣ Gauge

- **Definition**: 
A value that can go up or down at any time. Represents the current state.

- **Use Cases**:
- CPU usage  - Memory usage  -Temperature   -Queue length
- Example: node_memory_Active_bytes
Important: **Unlike counters, gauges can be set, increased, or decreased.**

## 3️⃣ Histogram

- **Definition**: 
Samples observations (like request durations, response sizes) into buckets and counts them.
Provides:
- Count of observations
- Sum of observed values
- Count per bucket

- **Use Cases**:
- Request latency distributions
- Response size distributions

## 4️⃣ Summary

- **Definition**: 
Similar to Histogram, but instead of buckets, it computes quantiles (e.g., 0.5 = median, 0.95 = 95th percentile) client-side.
Also provides total count and sum.

- **Use Cases**:
- Request durations
- Response sizes
Important:
- Summary is calculated at the client/exporter side.
- Histogram is more flexible when doing aggregations across instances.

| Metric Type   | Increases Only | Goes Up/Down | Quantiles/Percentiles | Best For                        |
| ------------- | -------------- | ------------ | --------------------- | ------------------------------- |
| **Counter**   | ✅ Yes          | ❌ No         | ❌ No                  | Event counts (requests, errors) |
| **Gauge**     | ❌ No           | ✅ Yes        | ❌ No                  | Current values (CPU, memory)    |
| **Histogram** | ❌ No           | ✅ Yes        | ✅ Yes (via PromQL)    | Distributions (latency, sizes)  |
| **Summary**   | ❌ No           | ✅ Yes        | ✅ Yes (client-side)   | Percentiles per instance        |

✅ In Short
- **Counter** → Always increasing, used for counting events.
- **Gauge** → Goes up & down, used for current values.
- **Histogram** → Measures distributions with buckets, good for aggregation.
- **Summary** → Similar to Histogram but computes quantiles at source.
