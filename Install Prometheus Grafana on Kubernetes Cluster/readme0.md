# Create EKS Cluster
```
eksctl create cluster -f create_cluster.yaml
```
# Install Prometheus
- 1
```
helm repo add prometheus-community \
https://prometheus-community.github.io/helm-charts
```
- 2
```
helm repo update
```
- 3
```
helm search repo kube-prometheus-stack --max-col-width 23
```
- 4
```
helm install monitoring \
prometheus-community/kube-prometheus-stack \
--values prometheus-values.yaml \
--version 16.10.0 \
--namespace monitoring \
--create-namespace
```
- 5
```
kubectl get pods -n monitoring
```
- 6
```
kubectl get svc -n monitoring 
```
- 7
```
kubectl port-forward \
svc/monitoring-kube-prometheus-prometheus 9090 \
-n monitoring
```
- queries: All pods so far: kube_pod_created

- According to Namespace ^^:count by (namespace) (kube_pod_created)

- Current running pods: sum by (namespace) (kube_pod_info)

- Non-ready pods "by namespace": sum by (namespace) (kube_pod_status_ready{condition="false"})

- 8 Configure to see EKS meterics localy.
```
kubectl get cm kube-proxy-config -n kube-system -o yaml
kubectl get cm kube-proxy-config -n kube-system -o yaml > kube-proxy-config.yaml
```
- 9
```
kubectl -n kube-system get cm kube-proxy-config -o yaml |sed 's/metricsBindAddress: 127.0.0.1:10249/metricsBindAddress: 0.0.0.0:10249/' | kubectl apply -f -
```
- 10
```
watch -n 1 -t kubectl get pods -n kube-system
```
- 11
```
kubectl -n kube-system patch ds kube-proxy -p "{\"spec\":{\"template\":{\"metadata\":{\"labels\":{\"updateTime\":\"`date +'%s'`\"}}}}}"
```
- To check targets and alerts go to http://localhost:9090 
- 12 For use Grafana Dashboard
```
kubectl port-forward \
svc/monitoring-grafana 3000:80 \
-n monitoring 
```
- Open following dashboards
Kubernetes / Compute Resources / Cluster
Kubernetes / Kubelet
USE Method / Cluster
Nodes

# Deploy Postgres Helm Chart
```
helm repo add bitnami https://charts.bitnami.com/bitnami
```
- 
```
helm repo update
```
- 
```
helm search repo postgresql --max-col-width 23
```
- Updated postgres-values.yaml and save it in the directory to use it
```
# postgres-values.yaml
postgresqlDatabase: test  # line 155
metrics:                  # line 734
  enabled: true
serviceMonitor:           # line 744
  enabled: false
  additionalLabels:
    prometheus: devops
```
- 
```
helm install postgres \
bitnami/postgresql \
--values postgres-values.yaml \
--version 10.5.0 \
--namespace db \
--create-namespace
```
- check the pods
```
kubectl get pods -n db
```

# Create Service Monitor for Postgres
- Create service-monitor.yaml file is in the older
```
---
# https://github.com/prometheus-operator/prometheus-operator
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: postgres-postgresql
  namespace: db
  labels:
    prometheus: devops
spec:
  endpoints:
  - port: <port>
    interval: 60s
    scrapeTimeout: 30s
  namespaceSelector:
    matchNames:
    - <namespace>
  selector:
    matchLabels:
      <labels>
```
- 
```
kubectl get prometheus \
monitoring-kube-prometheus-prometheus \
-o yaml \
-n monitoring
```
- get endpoint
```
kubectl get endpoints -n db
```
- 
```
kubectl get services -n db
```
- 
```
kubectl describe endpoints postgres-postgresql-metrics -n db
```
- 
```
kubectl apply -f service-monitor.yaml
```
- Import Grafana dashboard 9628
# Clean up 
```
helm repo remove prometheus-community bitnami
helm uninstall monitoring -n monitoring
helm uninstall postgres -n db
kubectl delete crd alertmanagerconfigs.monitoring.coreos.com
kubectl delete crd alertmanagers.monitoring.coreos.com
kubectl delete crd podmonitors.monitoring.coreos.com
kubectl delete crd probes.monitoring.coreos.com
kubectl delete crd prometheuses.monitoring.coreos.com
kubectl delete crd prometheusrules.monitoring.coreos.com
kubectl delete crd servicemonitors.monitoring.coreos.com
kubectl delete crd thanosrulers.monitoring.coreos.com
```
- Delete the cluster
```
eksctl delete cluster -f create_cluster.yaml
```