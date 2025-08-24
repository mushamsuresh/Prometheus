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
------------------------------------------------------------------------------------------------------------------
# 📤 Prometheus Exporters Basics

**Exporters** are components that expose metrics from a system, service, or application in a format Prometheus can scrape (`/metrics`). They act as **bridges** between Prometheus and systems that do not natively provide metrics.

---

## 1️⃣ Node Exporter
- **Purpose**: Exposes **hardware and OS-level metrics**.  
- **Metrics Example**:
  - CPU usage: `node_cpu_seconds_total`
  - Memory: `node_memory_Active_bytes`
  - Disk: `node_disk_io_time_seconds_total`
  - Network: `node_network_receive_bytes_total`
- **Use Case**: Monitoring the **health and performance of servers** (physical or virtual machines).

---

## 2️⃣ cAdvisor / kube-state-metrics
### cAdvisor
- **Purpose**: Collects **container-level metrics**.  
- **Metrics Example**:
  - CPU and memory usage per container
  - Container restarts
- **Use Case**: Useful in **Docker** or **Kubernetes** environments to monitor container resource usage.

### kube-state-metrics
- **Purpose**: Exposes **Kubernetes object state** (deployments, pods, nodes, replicas, etc.).  
- **Metrics Example**:
  - `kube_deployment_status_replicas_available`
  - `kube_pod_status_phase`
- **Use Case**: Monitoring **Kubernetes cluster health** and deployments, independent of container resource metrics.

---

## 3️⃣ Blackbox Exporter
- **Purpose**: Performs **external probing** of endpoints.  
- **Supported Protocols**: HTTP, HTTPS, DNS, TCP, ICMP (ping)  
- **Metrics Example**:
  - Response time
  - Availability (up/down)
  - HTTP status codes
- **Use Case**: Monitoring **service availability**, uptime, and network reachability.

---

## 4️⃣ Custom Exporters
- **Purpose**: Create your own exporter when no standard exporter exists.  
- **How it Works**:
  1. Your application exposes metrics (usually via HTTP `/metrics`).  
  2. Follow **Prometheus metric format**: `metric_name{labels} value timestamp`  
  3. Prometheus scrapes it like any other target.
- **Use Case**: Monitoring **internal apps, legacy systems, or specialized hardware**.

---

## ✅ Key Points
- Exporters **translate raw data into Prometheus metrics format**.  
- Prometheus is **pull-based**, so exporters expose an endpoint instead of sending metrics.  
- Common exporters cover **system metrics, container metrics, application metrics, and network/service checks**.  
- Custom exporters let you **monitor anything not supported by default exporters**.
---
## 🚨 Prometheus Alerting Basics
 Prometheus has a **robust alerting system** that works with **Alertmanager** to notify you about problems in your infrastructure or applications.
---
## 1️⃣ Alert Rules in Prometheus
- Defined in **YAML files** (commonly `rules.yml`) or inline in `prometheus.yml`.
- Use **PromQL expressions** to define conditions for alerts.
## 2️⃣ Connecting Alertmanager
- Prometheus sends alerts to Alertmanager when the alert conditions are met.
- Alertmanager handles routing, silencing, grouping, and notification delivery.
## 3️⃣ Alert Routing
Alertmanager can send notifications to multiple channels:
- **Slack, Email, PagerDuty, Opsgenie, Webhook (custom)**
Routing rules determine which alerts go where based on labels (e.g., severity, team).
## 4️⃣ Silencing, Grouping, and Inhibition
- **Silencing**: Temporarily mutes alerts for maintenance or known issues.
- Example: silence CPU alerts for a specific server during upgrade.
- **Grouping**: Combines similar alerts into a single notification. Reduces alert noise.
- Example: multiple nodes down → one alert for the cluster.
- **Inhibition**: Suppresses alerts if another alert is already firing.
- Example: if a cluster down alert is active, node-specific down alerts are inhibited.
