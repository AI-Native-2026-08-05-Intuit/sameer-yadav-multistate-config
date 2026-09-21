# Prometheus Adapter (W6D5 Task 2)

`overlays/dev/50-hpa.yaml`'s HPA reads a custom metric
(`multistate_inflight_requests`) from the `custom.metrics.k8s.io` API.
That API is served by the Prometheus Adapter, which is Helm-installed
directly against the cluster (not GitOps-synced by Argo CD, same as KEDA
itself is a cluster-level operator install, not a per-namespace app):

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm upgrade --install prometheus-adapter prometheus-community/prometheus-adapter \
  --namespace monitoring \
  -f prometheus-adapter-values.yaml
```

`prometheus-adapter-values.yaml` at the repo root holds the rule mapping
`multistate_inflight_requests` (a Micrometer gauge the app repo's
`InflightRequestsGauge` filter exposes on `/actuator/prometheus`, scraped
by the app repo's existing W5D5 `ServiceMonitor` with no new scrape
config) into the custom-metrics API the HPA above queries.
