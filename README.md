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
Step 3 : 
**Monitor otel-demo application using prometheus**

```
helm upgrade prometheus prometheus-community/prometheus -n monitoring -f helm/prometheus/values.yaml
```
step 4 :
**Install basic otel collector as daemonset** 

```
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo update
helm install otel-collector open-telemetry/opentelemetry-collector -n observability --create-namespace -f helm/otel-collector/values.yaml

```
step 5 :
**add prometheus receiver and prometheusremotewrite exporter to mimic the promethus setup done earlier**

```
helm upgrade otel-collector open-telemetry/opentelemetry-collector -n observability -f helm/otel-collector/values-prometheus.yaml
```
step 6 : 
**remove prometheus scrapping to avoid duplication**

```
helm upgrade prometheus prometheus-community/prometheus -n monitoring -f helm/prometheus/values-no-job.yaml
```
