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






