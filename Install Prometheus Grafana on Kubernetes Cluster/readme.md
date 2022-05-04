# Initialize Helm
```
helm init --stable-repo-url=https://charts.helm.sh/stable --wait --tiller-image ghcr.io/helm/tiller:v2.8.2
``` 
- cloned the Kubernetes charts repo:
```
git clone https://github.com/kubernetes/charts
cd charts
git checkout efdcffe0b6973111ec6e5e83136ea74cdbe6527d
```
- see the message 
- "HEAD is now at efdcffe... [stable/prometheus] Ability to enable admin API (#5570)"
# Create EKS Cluster
```
eksctl create cluster -f create_cluster.yaml
```
- We are using EKS managed nodes controlplan is abstracted. we don't need to monitor etcd, kube-controller, kube-scheudler
- use prometheus-setvalues.yml to update variables 
# Deploy Prometheus Stack Helm Chart
- Add prometheus repo
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```
- update helm repo 
```
helm repo update
```
Find prometheus-prometheus-stack 
```
helm search repo kube-prometheus-stack --max-col-width 23
```
- Install prometheus use helm chart set version 16 and use prometheus-setvalues.yml
```
helm install monitoring \
prometheus-community/kube-prometheus-stack \
--values prometheus-values.yaml \
--version 16.10.0 \
--namespace monitoring \
--create-namespace
```
- See all of the pods in the namespace monitoring
```
kubectl get pods -n monitoring
kubectl get svc -n monitoring 
```
- Use port-forward to access prometheus
```
kubectl port-forward \
svc/monitoring-kube-prometheus-prometheus 9090 \
-n monitoring
```



















