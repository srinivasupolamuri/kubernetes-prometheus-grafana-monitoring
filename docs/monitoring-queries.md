# Monitoring and Prometheus Queries

## 1. Confirm cluster resources

```bash
kubectl get nodes
kubectl get pods -A
kubectl get services -A
kubectl get deployments -A
```

## 2. Check Prometheus targets

Open Prometheus and select **Status → Targets**. Confirm that the intended scrape targets are healthy. A running Pod alone does not guarantee a scrape target is healthy.

## 3. Example PromQL queries

Check target status:

```promql
up
```

List Kubernetes Pod information, if kube-state-metrics is scraped:

```promql
kube_pod_info
```

Available replicas by Deployment, if this metric is present:

```promql
kube_deployment_status_replicas_available
```

Check CPU usage metrics if the relevant exporter and metric are available:

```promql
rate(node_cpu_seconds_total{mode!="idle"}[5m])
```

Metrics vary by exporter, chart configuration, and scrape settings. If a query returns no data, first verify that the metric exists and the target is healthy.

## 4. Grafana

1. Open Grafana.
2. Verify the Prometheus data source connection.
3. Open the configured dashboard.
4. Choose a time range that includes collected samples.
5. If a panel shows no data, run its query directly in Prometheus and check its label filters.

## 5. Raw kube-state-metrics endpoint

```bash
kubectl port-forward service/prometheus-kube-state-metrics 8080:8080
```

Browse to `http://localhost:8080/metrics`. This displays the endpoint's current raw metrics; Prometheus queries show the metrics that Prometheus has scraped and retained.
