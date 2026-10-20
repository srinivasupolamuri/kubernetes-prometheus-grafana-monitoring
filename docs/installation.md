# Installation and Setup

This guide documents the common setup for a fresh Minikube environment. The original working environment already had Prometheus, Grafana, kube-state-metrics and node-exporter installed, so do not repeat the installation there.

## 1. Prerequisites

Verify the required tools:

```bash
docker --version
minikube version
kubectl version --client
helm version
```

Start Minikube if needed:

```bash
minikube start
kubectl get nodes
kubectl cluster-info
```

## 2. Check whether monitoring is already installed

Always check before installing anything:

```bash
helm list -A
kubectl get pods -A
kubectl get services -A
kubectl get deployments -A
```

The working environment documented in this project showed Services named `prometheus-server`, `grafana`, `prometheus-kube-state-metrics`, and `prometheus-prometheus-node-exporter`.

## 3. Example installation using Helm (fresh environment only)

The following is a common example for installing the Prometheus community chart and a separate Grafana chart. Chart defaults and generated resource names may change; verify them after installation. Do not run these commands against an existing release without reviewing `helm list -A` first.

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

Install Prometheus and its chart-managed exporters:

```bash
helm install prometheus prometheus-community/prometheus
```

Install Grafana separately:

```bash
helm install grafana grafana/grafana
```

Verify resources:

```bash
helm list -A
kubectl get pods -A
kubectl get services -A
kubectl get deployments -A
```

If your original setup used a different release name, chart version, values file, or installation method, document that exact command instead. This example is not a claim that these were the commands used in the existing cluster.

## 4. Access Prometheus

```bash
kubectl port-forward service/prometheus-server 9090:80
```

Open `http://localhost:9090` and check **Status → Targets**.

## 5. Access Grafana

In another terminal:

```bash
kubectl port-forward service/grafana 3000:80
```

Open `http://localhost:3000`. Sign in using the credentials configured for your installation. Configure Prometheus as a data source if it is not already configured.

## 6. Inspect kube-state-metrics

```bash
kubectl port-forward service/prometheus-kube-state-metrics 8080:8080
```

Open `http://localhost:8080/metrics` to view raw metrics. This is not the Prometheus query UI.

## 7. WSL / Windows browser access

By default, port-forward binds to localhost. To access through the WSL network interface from Windows, use a trusted local development network and run:

```bash
kubectl port-forward --address 0.0.0.0 service/prometheus-server 9090:80
```

For Grafana:

```bash
kubectl port-forward --address 0.0.0.0 service/grafana 3000:80
```

Use the current WSL IP address in the Windows browser. Binding to `0.0.0.0` can expose the forwarded service to other reachable machines, so stop the process when finished and avoid doing this on untrusted networks.
