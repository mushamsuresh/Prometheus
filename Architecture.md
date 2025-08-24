## 📊 Prometheus Server

The **Prometheus Server** is the **core component** of the Prometheus monitoring system.  
It acts as the **brain** that collects, stores, and makes monitoring data available for querying and alerting.

---

## 🔑 What the Prometheus Server Does

### 1. Scraping Metrics
- The server connects to different **targets** (applications, exporters, services, etc.) over HTTP and pulls metrics at regular intervals.  
- Example:  
- http://node-exporter:9100/metrics

### 2. Storing Data
- Stores scraped metrics in a **time-series database (TSDB)** optimized for monitoring workloads.  
- Every metric has a **timestamp**, **value**, and **labels**.

### 3. Querying Data
- Provides a query language called **PromQL (Prometheus Query Language)** to analyze the stored data.  
- Used in dashboards (like Grafana) or directly in the Prometheus UI.

### 4. Alerting
- Based on **PromQL expressions**, the server can trigger alerts.  
- Alerts are sent to the **Alertmanager** for routing to channels such as email, Slack, PagerDuty, etc.

### 5. Visualization
- Has a built-in web UI to view metrics and run queries.  
- Commonly paired with **Grafana** for richer, more interactive dashboards.

---

## ⚙️ Prometheus Server Architecture

- **Prometheus Server**
- Scraper (collects metrics)
- Time-Series Database (stores metrics)
- PromQL Engine (query & analyze)

- **Targets / Exporters**
- Applications, OS, or services exposing `/metrics`

- **Alertmanager**
- Handles alerts and routes them to notification systems

- **Grafana (optional)**
- Dashboarding and visualization tool

---

## ✅ In Short
The **Prometheus Server** is a **monitoring and alerting engine** that:
- **Collects** time-series metrics,  
- **Stores** them efficiently,  
- **Lets you query** them using PromQL,  
- **Generates alerts** when conditions are met,  
- **Supports visualization** through its UI or external tools like Grafana.

 # ⏱️ TSDB in Prometheus Server

In Prometheus, **TSDB** stands for **Time Series Database**.  
It is the **storage engine inside the Prometheus server** that stores all scraped metrics as **time series data**.

---

## 🔑 What is Time Series Data?
- A **time series** is simply a sequence of values associated with timestamps.  
- In Prometheus: metric_name{label1="value1", label2="value2"} => [ (timestamp1, value1), (timestamp2, value2), ... ]
- 
- Example:  http_requests_total{method="GET", status="200"}
- could have values over time: (1692873600, 1024), (1692873660, 1048), (1692873720, 1060) ...

---

## ⚙️ How TSDB Works in Prometheus

1. **Write Path (Ingestion)**
   - Metrics scraped from targets are written to **WAL (Write-Ahead Log)** for durability.
   - Then stored in memory as **in-memory chunks**.

2. **Block Storage**
   - Every 2 hours, in-memory data is compacted and written as an immutable **block** on disk.
   - Each block contains:
     - **Chunks** (time series samples)
     - **Index** (fast lookup of metrics & labels)
     - **Metadata** (block info)

3. **Compaction & Retention**
   - Older blocks are compacted into larger ones to save space.
   - Data retention is controlled by:
     ```
     --storage.tsdb.retention.time=15d
     ```
     (default: 15 days)

4. **Query Path**
   - PromQL queries run against TSDB blocks and in-memory chunks.
   - Provides fast and efficient time-series queries.

---

## 📦 TSDB Storage Layout
On disk, Prometheus stores data in a directory like:
/data/
├── wal/ # Write-Ahead Log
├── 01F3ABCD1234/ # Block folder
│ ├── chunks/ # Actual data chunks
│ ├── index # Index file
│ └── meta.json # Metadata
└── 01F3BCDE5678/ # Another block

---

## ✅ In Short
- **TSDB = Time Series Database** inside Prometheus.  
- Stores metrics with **labels + timestamp + value**.  
- Uses **WAL, chunks, blocks, and compaction** for efficiency.  
- Powers all Prometheus queries and alerts.  
# 📤 Exporters in Prometheus

In Prometheus, an **Exporter** is a **tool or agent** that exposes metrics in a format Prometheus understands (`/metrics` endpoint).  
Since Prometheus uses a **pull model** (it scrapes metrics over HTTP), **exporters bridge the gap** between Prometheus and systems/applications that don’t natively expose metrics.

---

## 🔑 What Exporters Do
- **Collect metrics** from an application, OS, or service.  
- **Convert them** into Prometheus-compatible format (usually plain text over HTTP).  
- **Expose** them on an endpoint (commonly `/metrics`) for Prometheus to scrape.

---

## ⚙️ How Exporters Work
1. Application / service generates raw stats (CPU, memory, requests, etc.).
2. Exporter translates these stats into Prometheus metrics format: metric_name{label1="value1", ...} value
3. Prometheus scrapes the exporter endpoint on a schedule.

---

## 🛠️ Common Types of Exporters

### 1. **Node Exporter**
- Exposes hardware and OS metrics (CPU, memory, disk, network, etc.).
- Example endpoint:  http://localhost:9100/metrics

### 2. **Database Exporters**
- **MySQL Exporter**, **Postgres Exporter**, **MongoDB Exporter**, etc.  
- Provide metrics about queries, cache, replication, performance.

### 3. **Application Exporters**
- **NGINX Exporter**, **Apache Exporter**, **HAProxy Exporter**, etc.  
- Collect metrics from web servers, load balancers, etc.

### 4. **Cloud / Infrastructure Exporters**
- **Blackbox Exporter** → probes endpoints (HTTP, DNS, TCP, ICMP).  
- **SNMP Exporter** → collects metrics from network devices.  
- **cAdvisor Exporter** → container-level metrics (used in Kubernetes).

---

## ✅ In Short
- **Exporters = metric adapters for Prometheus.**  
- They **collect, translate, and expose metrics** in the Prometheus format.  
- Without exporters, Prometheus could not monitor many systems.  
- Node Exporter is the **most commonly used** exporter for infrastructure metrics.

---

# 🚨 Alertmanager in Prometheus

The **Alertmanager** is a core component in the Prometheus ecosystem that is responsible for **handling alerts** generated by the Prometheus server.

Prometheus itself can **evaluate alerting rules** (written in **PromQL**) and generate alerts when conditions are met.  
However, **managing, grouping, silencing, and routing alerts** is done by the **Alertmanager**.

---

## 🔑 What Alertmanager Does

1. **Receives Alerts**
   - Prometheus server evaluates alerting rules and sends alerts to Alertmanager.

2. **Deduplication**
   - Removes duplicate alerts to avoid noise when multiple Prometheus servers send the same alert.

3. **Grouping**
   - Groups related alerts into a single notification.  
   - Example: If multiple nodes in a cluster are down, it sends one grouped alert instead of hundreds.

4. **Inhibition**
   - Suppresses alerts if certain other alerts are already firing.  
   - Example: If a "cluster down" alert is active, individual "node down" alerts can be silenced.

5. **Silencing**
   - Allows users to mute certain alerts for a period of time (e.g., during maintenance).

6. **Routing**
   - Decides **where alerts go** (email, Slack, PagerDuty, Opsgenie, Webhook, etc.).

---

## ⚙️ How Alertmanager Works

### Flow: [ Prometheus Server ] --alerts--> [ Alertmanager ] --routes--> [ Notification Channels ]


- **Prometheus**: Defines *alerting rules* in `prometheus.yml`  
- **Alertmanager**: Handles the logic of managing and sending alerts  
- **Notification Channels**: Email, Slack, Teams, PagerDuty, etc.  

---

## 📂 Alertmanager Configuration Example

```yaml
global:
  resolve_timeout: 5m

route:
  receiver: 'team-slack'

receivers:
  - name: 'team-slack'
    slack_configs:
      - channel: '#alerts'
        send_resolved: true
✅ In Short

Alertmanager = Alert router and manager for Prometheus.
Handles:
- Deduplication
- Grouping
- Inhibition
- Silencing
- Routing to notification channels
Ensures alerting is reliable, meaningful, and not noisy.

# 🔎 Service Discovery in Prometheus

**Service Discovery (SD)** in Prometheus is the mechanism by which Prometheus **automatically finds targets (endpoints to scrape metrics from)** without requiring manual configuration of every target.

In dynamic environments like **Kubernetes, Docker, or cloud platforms (AWS, GCP, Azure)**, services and IPs keep changing. Service Discovery ensures that Prometheus can **adapt to these changes** and always scrape the correct targets.

---

## 🔑 Why Service Discovery?
- **Static configs don’t scale** → In cloud-native systems, new services may be created or destroyed frequently.  
- **Automation** → Prometheus updates its scrape targets automatically when new instances appear or old ones disappear.  
- **Flexibility** → Works across VMs, containers, orchestration systems, and bare-metal servers.

---

## ⚙️ How Service Discovery Works
1. Prometheus is configured with **service discovery integrations** in `prometheus.yml`.  
2. Prometheus queries the SD mechanism (like Kubernetes API, EC2 API, Consul, etc.).  
3. Discovered endpoints are added to the scrape target list dynamically.  
4. Prometheus scrapes metrics from those targets at defined intervals.

---

## 📂 Example: Kubernetes Service Discovery
Prometheus config (`prometheus.yml`):
```yaml
scrape_configs:
  - job_name: 'kubernetes-nodes'
    kubernetes_sd_configs:
      - role: node
Here, Prometheus discovers all Kubernetes nodes through the Kubernetes API, no need to list each node manually.
🛠️ Types of Service Discovery in Prometheus
1. Cloud Providers
- AWS EC2, GCP, Azure, OpenStack → discover VM instances.
2. Orchestration Systems
- Kubernetes (Pods, Services, Endpoints, Nodes, Ingress)
- Marathon, Nomad, ECS, etc.
3. Service Registries
- Consul, Etcd, Zookeeper.
4. Static Configs
- Simple, manual target definition (used for testing or small setup

✅ In Short

- Service Discovery = Dynamic target detection in Prometheus.
- Eliminates manual target management.
- Essential for cloud-native and containerized environments.
- Supported across clouds, Kubernetes, service registries, and more.
# 🔄 Push vs Pull Model in Monitoring (Prometheus Context)

Prometheus is based on the **pull model**, but understanding the **difference between push and pull models** is key in monitoring systems.

---

## 🟢 Pull Model (Used by Prometheus)

### How it Works:
- Prometheus **scrapes metrics** from targets (applications, exporters, services) at regular intervals.
- Targets expose metrics on an HTTP endpoint (usually `/metrics`).
- Prometheus pulls data whenever it needs.

### ✅ Advantages:
- Prometheus controls the scraping schedule → consistent data collection.
- Easier debugging (just curl the `/metrics` endpoint).
- Scalable in large dynamic environments (with **service discovery**).
- No need to give external systems network access to Prometheus.

### ❌ Disadvantages:
- Requires targets to expose an endpoint.
- Firewalled or short-lived jobs may be hard to scrape.

---

## 🔴 Push Model

### How it Works:
- Applications **push metrics** to a central server or gateway.
- Instead of Prometheus pulling, the client sends data when it wants.

### ✅ Advantages:
- Works better for **short-lived jobs** (that start and stop quickly).
- Useful when Prometheus cannot directly reach the targets (firewalls, NAT, etc.).

### ❌ Disadvantages:
- Monitoring system has less control over data frequency.
- Can lead to inconsistent or missing metrics.
- Harder to manage retries and failures.

---

## 📌 Prometheus Approach
- Prometheus uses **pull by default**.  
- For special cases (like **short-lived batch jobs**), Prometheus provides the **Pushgateway**:
  - Applications push their metrics to the Pushgateway.
  - Prometheus then scrapes the Pushgateway.

---

## 📊 Comparison Table

| Feature              | Pull Model (Prometheus default) | Push Model |
|-----------------------|---------------------------------|------------|
| **Data flow**         | Prometheus → target (scrapes)  | Target → server (pushes) |
| **Control**           | Prometheus controls timing     | Client controls timing |
| **Short-lived jobs**  | Harder to handle               | Easy to handle |
| **Debugging**         | Simple (check `/metrics`)      | Harder (need logs/push checks) |
| **Typical usage**     | Prometheus, Grafana            | StatsD, some logging systems |

---

## ✅ In Short
- **Pull Model** = Prometheus fetches metrics → preferred for consistency, scalability, and observability.  
- **Push Model** = Clients send metrics → used in special cases (short-lived jobs, restricted networks).  
- Prometheus primarily uses **pull**, with **Pushgateway** as an exception.
# 📝 PromQL (Prometheus Query Language)

**PromQL** stands for **Prometheus Query Language**.  
It is a powerful, flexible language built into Prometheus to **query, aggregate, and analyze time-series data** stored in the Prometheus server.

PromQL is used in:
- **Prometheus Web UI** → ad-hoc queries
- **Grafana** → dashboards
- **Alerting Rules** → triggering alerts based on conditions

---

## 🔑 Key Features of PromQL

1. **Selects time-series** using metric names and labels.  
   - Example:  
     ```promql
     http_requests_total{method="GET", status="200"}
     ```

2. **Supports arithmetic & functions** to transform data.  
   - Example:  
     ```promql
     rate(http_requests_total[5m])
     ```
     → calculates per-second increase over last 5 minutes.

3. **Aggregation** for summaries across labels.  
   - Example:  
     ```promql
     sum(rate(http_requests_total[5m])) by (method)
     ```
     → total requests per second, grouped by HTTP method.

4. **Time range queries** → to plot graphs over time.  
   - Example:  
     ```promql
     node_cpu_seconds_total{mode="user"}
     ```

5. **Boolean and comparisons** for alerting.  
   - Example:  
     ```promql
     node_memory_Active_bytes / node_memory_MemTotal_bytes > 0.9
     ```
     → triggers when memory usage is above 90%.

---

## 📂 Types of PromQL Expressions

1. **Instant Vector**
   - A set of time series containing a single sample per series.
   - Example:
     ```promql
     up
     ```

2. **Range Vector**
   - A set of time series containing a range of data points.
   - Example:
     ```promql
     rate(http_requests_total[5m])
     ```

3. **Scalar**
   - A single numerical value.
   - Example:
     ```promql
     5 * 100
     ```

4. **String**
   - Rarely used, just literal strings.

---

## 📊 Common PromQL Functions

| Function                  | Purpose |
|----------------------------|---------|
| `rate()`                   | Per-second average rate of increase |
| `irate()`                  | Instantaneous rate of increase |
| `increase()`               | Total increase over a time range |
| `sum()` / `avg()` / `max()`| Aggregations |
| `histogram_quantile()`     | Calculate quantiles from histograms |
| `predict_linear()`         | Predict future values |

---

## ✅ In Short
- **PromQL = Query language for Prometheus**.  
- Lets you **filter, aggregate, and analyze** time-series metrics.  
- Used for **monitoring dashboards, troubleshooting, and alerting rules**.  
- Makes Prometheus extremely powerful in handling real-time monitoring data.


