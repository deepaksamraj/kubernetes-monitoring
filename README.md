# kubernetes-monitoring

## Overview

This repository provides a complete Kubernetes observability stack for a Flask application using Prometheus, Grafana, and ServiceMonitor.

- Flask app exposed with ServiceMonitor
- Prometheus scrapes metrics via ServiceMonitor
- Grafana visualizes metrics (dashboard JSON in `dashboards/flask-dashboard.json`)

## Architecture

```text
Flask App → ServiceMonitor → Prometheus → Grafana
```

```
[Flask] -> [ServiceMonitor] -> [Prometheus] -> [Grafana]
```

## Screenshot 
![Grafana_Dashboard_screenshot](/docs/image.png)


## Why Observability Matters

- You can’t fix what you can’t see.
- Observability gives you visibility into production behavior and root cause.
- Metrics + dashboards + alerts reduce MTTR and help track reliability targets.

## Helm vs Raw Manifests

### Helm
- Best for reusable installs and parameterization at deploy time.
- Use when you need configurable values (`values/*.yaml`) and environment-specific overrides.
- Ideal for CI/CD and multiple clusters.

### Raw Manifests
- Great for simple, explicit setup and learning.
- Use when you want full control over every Kubernetes object and immutability.
- Good for small demos or when avoiding templating complexity.

## Alert Rule

The alert rule in this repo detects the Flask app availability issue by monitoring request rate and error thresholds.

- source: `manifests/flask-servicemonitor.yaml` and Prometheus rules in chart/values (or wherever rule is configured)
- condition: high `http_errors` or low request throughput for a sustained period
- intent: catch service outages or 5xx spikes before users report issues

> (Adjust the specific metric / expression when you paste the exact rule from your Prometheus alert rules.)

## Further Enhancements

1. PagerDuty integration
   - Add Prometheus Alertmanager receiver for PagerDuty.
   - Use route grouping by service/component.

2. SLO Dashboards
   - Create service-level SLOs in Grafana (uptime, latency, error budget).
   - Add Prometheus recording rules for SLA/SLO calculations.

3. Log Aggregation with Loki
   - Collect logs with Promtail and send to Loki.
   - Build Grafana Explore dashboards and log-based alerts.

## Repository Structure

- `dashboards/flask-dashboard.json` - Grafana dashboard JSON document.
- `helm/values/grafana-values.yaml` - Grafana Helm values.
- `helm/values/prometheus-values.yaml` - Prometheus Helm values.
- `manifests/flask-servicemonitor.yaml` - ServiceMonitor config.
- `manifests/namespace.yaml` - Kubernetes namespace resources.

---

## Usage

1. Apply namespace:

```bash
kubectl apply -f manifests/namespace.yaml
```

2. Install Prometheus & Grafana with Helm:

```bash
helm upgrade --install prometheus prometheus-community/prometheus -f helm/values/prometheus-values.yaml

helm upgrade --install grafana grafana/grafana -f helm/values/grafana-values.yaml
```

3. Deploy Flask ServiceMonitor and Dashboard config.


