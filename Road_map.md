🔹 Prometheus (Metrics Collection & Alerting)
1. Core Concepts

What Prometheus is and why it’s used (metrics-based monitoring, pull model)

Architecture: Prometheus Server, TSDB (time series DB), Exporters, Service Discovery, Alertmanager

Pull vs Push model

PromQL (Prometheus Query Language) basics

2. Installation & Setup

Installing Prometheus (binary, Docker, Kubernetes Helm chart)

Configuring prometheus.yml (scrape configs, targets, jobs)

Setting retention & storage (local disk, remote storage like Thanos, Cortex, VictoriaMetrics)

3. Metrics Types

Counter → monotonically increasing (e.g., requests_total)

Gauge → goes up & down (e.g., memory_usage_bytes)

Histogram → observations in buckets (e.g., request duration)

Summary → similar to histogram but provides quantiles

4. PromQL (must practice)

Basic queries: rate(), increase(), irate()

Aggregations: sum(), avg(), max(), min(), count()

Grouping: by (label), without (label)

Functions: topk(), bottomk(), histogram_quantile()

5. Exporters

Node Exporter → system-level metrics (CPU, memory, disk)

cAdvisor / kube-state-metrics → Kubernetes metrics

Blackbox Exporter → HTTP, DNS, TCP probing

Custom exporters → writing your own

6. Alerting

Alert rules in Prometheus (rules.yml)

Connecting Alertmanager

Alert routing (Slack, Email, PagerDuty, Opsgenie)

Silencing, grouping, inhibition
