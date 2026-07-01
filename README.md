**Session 1 : Traditional Monitoring/Evolution of Monitoring** - 10 Mins

**Session 2 : K8s traditional Monitoring with Prometheus / Prometheus Overview** - 20 mins

**Lab 1 : Create playground & Install OpenTelemetry Demo Application** - 20 mins


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
**Lab 2 : Install Prometheus Server** - 15 mins

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

**Session 3 : Observability & OpenTelemetry** -  20 mins

**Session 4 : OpenTelemetry Pipelines  - Design Metric Pipleine** - 10 mins


**Lab 3 : Install basic otel collector as daemonset**  - 10 mins

```
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo update
helm install otel-collector open-telemetry/opentelemetry-collector -n observability --create-namespace -f helm/otel-collector/values.yaml

```
**lab 4 part 1 : add prometheus receiver and prometheusremotewrite exporter to mimic the promethus setup done earlier** - 15 mins

```
helm upgrade otel-collector open-telemetry/opentelemetry-collector -n observability -f helm/otel-collector/values-prometheus.yaml

// to fix the role issue, show the role being used by prometheus and use the same in otel-collector helm charts

helm upgrade otel-collector open-telemetry/opentelemetry-collector -n observability -f helm/otel-collector/values-prometheus-role.yaml

```
 : 
**lab 4 Part 2: remove prometheus scrapping to avoid duplication** - 10 mins

```
kubectl edit configmap -n monitoring prometheus-server

// remove the job kubernetes-nodes-cadvisor from the config map, save and restart the prometheus server deployment

```

**Session 5: OpenTelemetry Instrumentation for traces - Design Trace pipeline** - 15 mins

**Lab 5: Auto Instrument an application for traces** - 15 mins

```
kubectl -n otel-demo port-forward svc/frontend-proxy 8080:8080

http://localhost:8080/jaeger/ui/



```

**Session 6: OpenTelemetry Log Srapping - Design Log pipeline** - 10 mins

**Lab 6: Add filelog receiver and otlp exporter for log shipping** - 10 mins
