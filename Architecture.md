# 📊 Prometheus Server

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
