```
kubectl create ns monitoring

helm repo add influxdata https://helm.influxdata.com
helm repo add grafana https://grafana.github.io/helm-charts


helm dependency build ./monitoring-stack
helm install monitoring-stack ./monitoring-stack --namespace monitoring

helm upgrade --install monitoring-stack ./monitoring-stack --namespace monitoring

DASHBOARD ID 928

```