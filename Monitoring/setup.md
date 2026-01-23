# Kubernetes Monitoring

### Prerequisites
- Kubernetes cluster running (local, cloud, or KIND)
- Install Helm 3 From Script:
```bash

curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm version
```
Create monitoring namespace:

```bash

kubectl create namespace monitoring
```
Once Helm is set up properly, add the repository as follows:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo list
helm repo update
```

Set Service Port Configuration with the following values (from values.yaml):

```yaml
prometheus:
  service:
    type: NodePort
    nodePort: 30000
grafana:
  service:
    type: NodePort
    nodePort: 31000
```
```bash
helm install prometheus-stack prometheus-community/kube-prometheus-stack -f values.yaml -n monitoring
```

Or Use Shorthand Combo

```bash
helm install prometheus-stack prometheus-community/kube-prometheus-stack -n monitoring --set prometheus.service.nodePort=30000,prometheus.service.type=NodePort,grafana.service.nodePort=31000,grafana.service.type=NodePort
```
Check Prometheus and Grafana pods

```bash
kubectl get pods -n monitoring
```
Check Prometheus and Grafana Services

```bash
kubectl get svc -n monitoring
```
Forward Port to access Prometheus, expose port 9090

```bash
kubectl port-forward svc/prometheus-stack-kube-prom-prometheus 9090:9090 -n monitoring --address=0.0.0.0 &
```
Forward Port to access Grafana, expose port 3000

```bash
kubectl port-forward svc/prometheus-stack-grafana 3000:80 -n monitoring --address=0.0.0.0 &
```
Login credentials for Grafana, user name admin by default and to fetch passwoord run below command
```bash
kubectl get secret prometheus-stack-grafana -n monitoring -o jsonpath="{.data.admin-password}" | base64 --decode
```
