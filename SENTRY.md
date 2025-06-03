```
helm repo add sentry https://sentry-kubernetes.github.io/charts
helm repo update

helm upgrade -i sentry sentry/sentry --namespace sentry --create-namespace --version 26.11.0 -f sentry.yaml 

kubectl exec -n sentry deploy/sentry-web -- sentry upgrade

```