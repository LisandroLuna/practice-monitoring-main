```
kubectl create ns monitoring

helm repo add influxdata https://helm.influxdata.com
helm repo add grafana https://grafana.github.io/helm-charts

helm dependency build ./monitoring-stack
helm install monitoring-stack ./monitoring-stack --namespace monitoring

helm upgrade --install monitoring-stack ./monitoring-stack --namespace monitoring

kubectl port-forward -n monitoring svc/monitoring-stack-grafana 3000:80

DASHBOARD ID 928

----------

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm upgrade --install prometheus -n monitoring prometheus-community/kube-prometheus-stack -f prometheus.yaml --version 62.7.0

kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-prometheus 9090:9090

```