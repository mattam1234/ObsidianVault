# Prometheus

**Prometheus** is an open-source monitoring and alerting toolkit designed for reliability and scalability. It's widely used for Kubernetes and cloud-native monitoring.

Project Homepage: [Prometheus](https://prometheus.io/)
Documentation: [Prometheus Documentation](https://prometheus.io/docs/)

---

## What is Prometheus?

Prometheus collects and stores metrics as time series data, recording information with timestamps. It's designed for monitoring dynamic cloud environments.

**Key Features:**
- **Multi-dimensional data model** - Metrics with key-value pairs
- **Powerful query language** - PromQL for data analysis
- **Pull-based collection** - Scrapes metrics from targets
- **Service discovery** - Automatic target discovery
- **Alerting** - Built-in alerting with Alertmanager
- **Visualization** - Integrates with Grafana

---

## Architecture

**Components:**
- **Prometheus Server** - Scrapes and stores metrics
- **Pushgateway** - For short-lived jobs
- **Alertmanager** - Handles alerts
- **Exporters** - Expose metrics from various systems
- **Client Libraries** - Instrument applications

**Data Flow:**
```
Application → Exporter → Prometheus → Grafana
                           ↓
                     Alertmanager → Notifications
```

---

## Installation

### Docker

```bash
# Run Prometheus in Docker
docker run -d \
  --name prometheus \
  -p 9090:9090 \
  -v /path/to/prometheus.yml:/etc/prometheus/prometheus.yml \
  prom/prometheus
```

### Kubernetes (Helm)

```bash
# Add Prometheus Helm repo
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Install Prometheus
helm install prometheus prometheus-community/prometheus \
  --namespace monitoring \
  --create-namespace

# Install kube-prometheus-stack (Prometheus + Grafana + Alertmanager)
helm install kube-prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace
```

### Kubernetes (Operator)

```bash
# Install Prometheus Operator
kubectl apply -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/main/bundle.yaml

# Create Prometheus instance
kubectl apply -f - <<EOF
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  name: prometheus
  namespace: monitoring
spec:
  serviceAccountName: prometheus
  serviceMonitorSelector:
    matchLabels:
      team: frontend
  resources:
    requests:
      memory: 400Mi
  enableAdminAPI: false
EOF
```

### Binary Installation

```bash
# Download Prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.45.0/prometheus-2.45.0.linux-amd64.tar.gz
tar xvfz prometheus-2.45.0.linux-amd64.tar.gz
cd prometheus-2.45.0.linux-amd64

# Run Prometheus
./prometheus --config.file=prometheus.yml
```

---

## Configuration

**prometheus.yml:**
```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s
  external_labels:
    cluster: 'production'

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets: ['alertmanager:9093']

# Load rules
rule_files:
  - "rules/*.yml"

# Scrape configurations
scrape_configs:
  # Prometheus itself
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  # Node exporter
  - job_name: 'node'
    static_configs:
      - targets: ['node-exporter:9100']

  # Application
  - job_name: 'myapp'
    static_configs:
      - targets: ['myapp:8080']
    metrics_path: '/metrics'

  # Kubernetes service discovery
  - job_name: 'kubernetes-pods'
    kubernetes_sd_configs:
      - role: pod
    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
        action: keep
        regex: true
```

---

## Metrics

### Metric Types

**Counter:**
```
# Monotonically increasing value
http_requests_total
```

**Gauge:**
```
# Value that can go up or down
memory_usage_bytes
temperature_celsius
```

**Histogram:**
```
# Observations in configurable buckets
http_request_duration_seconds
```

**Summary:**
```
# Similar to histogram with quantiles
rpc_duration_seconds
```

### Naming Convention

```
<metric_name>_<unit>_<type>

Examples:
- http_requests_total (counter)
- process_cpu_seconds_total (counter)
- node_memory_MemFree_bytes (gauge)
- http_request_duration_seconds (histogram)
```

---

## PromQL (Query Language)

### Basic Queries

```promql
# Instant vector - current value
http_requests_total

# With label filtering
http_requests_total{job="api", status="200"}

# Range vector - values over time
http_requests_total[5m]
```

### Operators

```promql
# Arithmetic
rate(http_requests_total[5m])
sum(rate(http_requests_total[5m]))

# Comparison
http_requests_total > 1000

# Logical
up{job="api"} and up{instance="prod"}
```

### Functions

```promql
# Rate (per second increase)
rate(http_requests_total[5m])

# Sum
sum(rate(http_requests_total[5m])) by (status)

# Quantile
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))

# Increase
increase(http_requests_total[1h])

# Average over time
avg_over_time(cpu_usage[5m])
```

### Aggregations

```promql
# Sum by label
sum by (status) (http_requests_total)

# Average
avg(cpu_usage) by (instance)

# Max/Min
max(memory_usage_bytes) by (pod)
min(disk_free_bytes)

# Count
count(up{job="api"} == 1)
```

---

## Exporters

### Node Exporter (System Metrics)

```bash
# Run Node Exporter
docker run -d \
  --name node-exporter \
  --net="host" \
  --pid="host" \
  -v "/:/host:ro,rslave" \
  quay.io/prometheus/node-exporter:latest \
  --path.rootfs=/host
```

**Key Metrics:**
- `node_cpu_seconds_total` - CPU usage
- `node_memory_MemAvailable_bytes` - Available memory
- `node_disk_io_time_seconds_total` - Disk I/O
- `node_network_receive_bytes_total` - Network traffic

### Blackbox Exporter (Probing)

```yaml
# blackbox.yml
modules:
  http_2xx:
    prober: http
    timeout: 5s
    http:
      valid_status_codes: [200]
      method: GET
```

**Prometheus Config:**
```yaml
scrape_configs:
  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]
    static_configs:
      - targets:
        - https://example.com
        - https://api.example.com
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: blackbox-exporter:9115
```

### Common Exporters

- **node_exporter** - Linux system metrics
- **blackbox_exporter** - Endpoint probing
- **mysqld_exporter** - MySQL metrics
- **postgres_exporter** - PostgreSQL metrics
- **redis_exporter** - Redis metrics
- **nginx-prometheus-exporter** - Nginx metrics
- **elasticsearch_exporter** - Elasticsearch metrics

---

## Alerting

### Alert Rules

**alert-rules.yml:**
```yaml
groups:
  - name: example
    interval: 30s
    rules:
      # High CPU usage
      - alert: HighCPUUsage
        expr: 100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High CPU usage on {{ $labels.instance }}"
          description: "CPU usage is {{ $value }}%"

      # Service down
      - alert: ServiceDown
        expr: up{job="api"} == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Service {{ $labels.job }} is down"
          description: "{{ $labels.instance }} has been down for more than 1 minute"

      # High memory usage
      - alert: HighMemoryUsage
        expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100 > 90
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High memory usage on {{ $labels.instance }}"
```

### Alertmanager

**alertmanager.yml:**
```yaml
global:
  resolve_timeout: 5m

route:
  group_by: ['alertname', 'cluster']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 12h
  receiver: 'default'
  routes:
    - match:
        severity: critical
      receiver: 'pagerduty'
    - match:
        severity: warning
      receiver: 'slack'

receivers:
  - name: 'default'
    email_configs:
      - to: 'team@example.com'

  - name: 'slack'
    slack_configs:
      - api_url: 'https://hooks.slack.com/services/XXX'
        channel: '#alerts'
        title: '{{ .GroupLabels.alertname }}'
        text: '{{ range .Alerts }}{{ .Annotations.description }}{{ end }}'

  - name: 'pagerduty'
    pagerduty_configs:
      - service_key: 'your-service-key'
```

---

## Kubernetes Monitoring

### ServiceMonitor

```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: myapp-monitor
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: myapp
  endpoints:
  - port: metrics
    interval: 30s
    path: /metrics
```

### PodMonitor

```yaml
apiVersion: monitoring.coreos.com/v1
kind: PodMonitor
metadata:
  name: pod-monitor
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: myapp
  podMetricsEndpoints:
  - port: metrics
    interval: 30s
```

### Common Kubernetes Metrics

```promql
# Pod CPU usage
sum(rate(container_cpu_usage_seconds_total[5m])) by (pod)

# Pod memory usage
sum(container_memory_usage_bytes) by (pod)

# Pod restarts
kube_pod_container_status_restarts_total

# Node capacity
kube_node_status_capacity

# Deployment replicas
kube_deployment_status_replicas_available
```

---

## Client Libraries

### Python

```python
from prometheus_client import Counter, Gauge, Histogram, start_http_server
import time

# Create metrics
requests = Counter('http_requests_total', 'Total HTTP requests', ['method', 'endpoint'])
in_progress = Gauge('requests_in_progress', 'Requests in progress')
duration = Histogram('request_duration_seconds', 'Request duration')

# Instrument code
@in_progress.track_inprogress()
@duration.time()
def process_request():
    requests.labels(method='GET', endpoint='/api').inc()
    time.sleep(1)

# Start metrics server
start_http_server(8000)
```

### Go

```go
package main

import (
    "github.com/prometheus/client_golang/prometheus"
    "github.com/prometheus/client_golang/prometheus/promhttp"
    "net/http"
)

var (
    httpRequests = prometheus.NewCounterVec(
        prometheus.CounterOpts{
            Name: "http_requests_total",
            Help: "Total HTTP requests",
        },
        []string{"method", "endpoint"},
    )
)

func init() {
    prometheus.MustRegister(httpRequests)
}

func main() {
    httpRequests.WithLabelValues("GET", "/api").Inc()
    http.Handle("/metrics", promhttp.Handler())
    http.ListenAndServe(":8080", nil)
}
```

---

## Grafana Integration

**Add Prometheus Data Source:**
1. Go to Configuration → Data Sources
2. Add Prometheus
3. URL: `http://prometheus:9090`
4. Save & Test

**Example Dashboard Query:**
```promql
# CPU Usage
100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Memory Usage
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100

# Request Rate
sum(rate(http_requests_total[5m])) by (status)
```

---

## Best Practices

✅ Use meaningful metric names and labels
✅ Don't create too many unique label combinations (cardinality)
✅ Set appropriate scrape intervals (balance freshness vs load)
✅ Use recording rules for expensive queries
✅ Implement proper alerting thresholds
✅ Monitor Prometheus itself
✅ Regular backups of Prometheus data
✅ Use service discovery in dynamic environments
✅ Document custom metrics
✅ Set retention policies appropriately

---

## Related Topics

- [[kubernetes/kubernetes|Kubernetes]] - Container orchestration
- [[tools/grafana|Grafana]] - Visualization
- [[monitoring/alertmanager|Alertmanager]] - Alert handling

---

## Troubleshooting

**High cardinality:**
```promql
# Check series count
prometheus_tsdb_symbol_table_size_bytes
count({__name__=~".+"})
```

**Missing metrics:**
```
- Check target status in Prometheus UI
- Verify scrape configuration
- Check network connectivity
- Examine application logs
```

**Performance issues:**
```
- Reduce scrape frequency
- Use recording rules
- Increase resources
- Check query complexity
```