```
kubectl create ns monitoring
helm dependency build ./monitoring-stack
helm install monitoring-stack ./monitoring-stack --namespace monitoring

helm upgrade --install monitoring-stack ./monitoring-stack --namespace monitoring

DASHBOARD ID 928

```