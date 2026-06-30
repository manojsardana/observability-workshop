Step 1 :
**Install OpenTelemetry Demo Application**

```
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts

helm repo update

helm install otel-demo open-telemetry/opentelemetry-demo --namespace otel-demo --create-namespace -f helm/otel-demo/values.yaml

 ```
**Verify, test and basic live monitoring** 

```
kubectl get pods -n otel-demo -w

kubectl --namespace otel-demo port-forward svc/frontend-proxy 8080:8080

kubectl top pod -n otel-demo

kubectl top node

kubectl logs deployment/frontend -n otel-demo

kubectl logs deployment/frontend -n otel-demo -f

kubectl get events -n otel-demo --sort-by=.lastTimestamp

kubectl --namespace otel-demo port-forward svc/frontend-proxy 8080:8080

```


Step 2 : 
**Install Prometheus Server**

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update

helm install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace
```
**Verify Prometheus UI and the jobs running by default**

```
kubectl --namespace monitoring port-forward svc/prometheus-server 8081:80

up

count by(job)(up)

count by(namespace)(kube_pod_info)

sum by(pod)(rate(container_cpu_usage_seconds_total{container!=""}[5m]))

topk(5, kube_pod_container_status_restarts_total)

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
