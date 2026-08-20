# Observability platform installation

Install these once per cluster, independently of the application release.

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add datadog https://helm.datadoghq.com
helm repo update

helm upgrade --install kube-prometheus-stack prometheus-community/kube-prometheus-stack \
  --namespace monitoring --create-namespace \
  -f observability/kube-prometheus-stack-values.yaml

helm upgrade --install datadog datadog/datadog \
  --namespace datadog --create-namespace \
  -f observability/datadog-values.yaml
```

Create `grafana-admin` and `datadog-api-key` through External Secrets or your secret manager before installing. Do not commit either credential.

Prometheus/Grafana/Alertmanager is the primary metrics and alerting path. Datadog provides infrastructure telemetry, logs, APM, and an optional second scrape path. Disable `datadog.prometheusScrape.enabled` if duplicate custom-metric ingestion is not desired.
