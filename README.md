Step 1 :
**Install OpenTelemetry Demo Application**

```
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts

helm repo update

helm install otel-demo open-telemetry/opentelemetry-demo --namespace otel-demo --create-namespace --f helm/values.yaml
 ```
Step 2 : 
**Install Prometheus Server**

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update

helm install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace
```
