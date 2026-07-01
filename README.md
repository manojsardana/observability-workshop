**Session 1 : Traditional Monitoring/Evolution of Monitoring** - 10 Mins

**Session 2 : K8s traditional Monitoring with Prometheus / Prometheus Overview** - 20 mins

**Lab 1 : Create playground & Install OpenTelemetry Demo Application** - 20 mins


```

helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts

helm repo update

helm install otel-demo open-telemetry/opentelemetry-demo --namespace otel-demo --create-namespace -f helm/otel-demo/values.yaml

helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/

helm repo update

helm upgrade --install metrics-server metrics-server/metrics-server -n kube-system

kubectl edit deployment metrics-server -n kube-system

// add to the args --kubelet-insecure-tls

 ```
**Verify, test and basic live monitoring** 

```
kubectl get pods -n otel-demo -w

kubectl --namespace otel-demo port-forward svc/frontend-proxy 8080:8080 --address 0.0.0.0

kubectl top pod -n otel-demo

kubectl top node

kubectl logs deployment/frontend -n otel-demo

kubectl logs deployment/frontend -n otel-demo -f

kubectl get events -n otel-demo --sort-by=.lastTimestamp


```
**Lab 2 : Install Prometheus Server** - 15 mins

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update

helm install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace -f helm/prometheus/values-no-job.yaml
```
**Verify Prometheus UI and the jobs running by default**

```
kubectl --namespace monitoring port-forward svc/prometheus-server 8081:80 --address 0.0.0.0

up

count by(job)(up)

count by(namespace)(kube_pod_info)

sum by(pod)(rate(container_cpu_usage_seconds_total{container!=""}[5m]))

topk(5, kube_pod_container_status_restarts_total)

up{job="kubernetes-nodes-cadvisor"}

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
kubectl -n otel-demo port-forward svc/frontend-proxy 8080:8080 --address 0.0.0.0

http://localhost:8080/jaeger/ui/

// update any of the service to point to collector deployed, update environment variable OTEL_COLLECTOR_NAME  to  otel-collector-opentelemetry-collector.observability.svc.cluster.local for frontend, frontend-proxy and any other microservices

kubectl set env deployment/frontend -n otel-demo OTEL_COLLECTOR_NAME=otel-collector-opentelemetry-collector.observability.svc.cluster.local

kubectl set env deployment/frontend-proxy -n otel-demo OTEL_COLLECTOR_NAME=otel-collector-opentelemetry-collector.observability.svc.cluster.local

kubectl set env deployment/ad -n otel-demo OTEL_COLLECTOR_NAME=otel-collector-opentelemetry-collector.observability.svc.cluster.local


// update the collector to send the data to jaeger endpoint
helm upgrade otel-collector open-telemetry/opentelemetry-collector -n observability -f helm/otel-collector/values-jaeger.yaml

```

**Session 6: OpenTelemetry Log Srapping - Design Log pipeline** - 10 mins

**Lab 6: Add filelog receiver and otlp exporter for log shipping** - 10 mins

```
helm upgrade otel-collector open-telemetry/opentelemetry-collector -n observability -f helm/otel-collector/values-filelog.yaml

helm install dashboards opensearch/opensearch-dashboards -n otel-demo --set opensearchHosts=http://opensearch:9200

kubectl set env deployment/dashboards-opensearch-dashboards -n otel-demo DISABLE_SECURITY_DASHBOARDS_PLUGIN=true

kubectl port-forward svc/dashboards-opensearch-dashboards -n otel-demo 5601:5601 --address 0.0.0.0

http://localhost:5601

```

