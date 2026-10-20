# Troubleshooting

## Prometheus page does not open

```bash
kubectl get service prometheus-server
kubectl get pods -A
kubectl port-forward service/prometheus-server 9090:80
```

Check that the Service name and port match the installed release and keep the port-forward terminal open.

## Grafana page does not open

```bash
kubectl get service grafana
kubectl get pods -A
kubectl port-forward service/grafana 3000:80
```

Check the Grafana Pod logs and events if needed:

```bash
kubectl logs deployment/grafana --tail=100
kubectl describe deployment grafana
```

Deployment names may differ; verify them with `kubectl get deployments -A`.

## Prometheus query returns no data

1. Open **Status → Targets** in Prometheus.
2. Confirm the expected target is configured and healthy.
3. Check whether the metric exists in the raw metrics endpoint.
4. Check the selected time range and query labels.
5. Verify kube-state-metrics or node-exporter is running and being scraped.

## Grafana dashboard shows “No data”

1. Test the Prometheus data source connection.
2. Run the panel query directly in Prometheus.
3. Check the dashboard time range and label filters.
4. Confirm the metric is exposed by an exporter and scraped.

## Windows browser cannot reach a forwarded WSL port

Default `kubectl port-forward` listens on localhost. For trusted local development, use `--address 0.0.0.0` and connect to the current WSL IP. This can expose the service to other reachable machines; stop the port-forward when finished.

## Useful diagnostic commands

```bash
kubectl get events -A --sort-by=.metadata.creationTimestamp
kubectl get pods -A -o wide
kubectl get services -A
kubectl describe pod <POD_NAME> -n <NAMESPACE>
kubectl logs <POD_NAME> -n <NAMESPACE> --tail=100
helm list -A
```
