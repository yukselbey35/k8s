# What is kiali
- Kiali is an observability console for Istio with service mesh configuration and validation capabilities
- Kiali is a management console for Istio service mesh. Which provides robust observability for our running mesh and number of more features.
- Kiali can automatically generate istio configuration
- Install Kiali
- Istio provides a basic sample installation to quickly get Kiali up and running
```
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.13/samples/addons/kiali.yaml
```

# Install Kiali Operator via Helm Chart
```
$ helm repo add kiali https://kiali.org/helm-charts
$ helm repo update kiali
$ helm install \
    --namespace kiali-operator \
    --create-namespace \
    kiali-operator \
    kiali/kiali-operator
```

- Get the Status of the Installation
```
kubectl get kiali kiali -n istio-system -o jsonpath='{.status}'
```

- it is served localhost:200001 port
- TO CHECK kiali deployment STATUS USE COMMAND 
```
kubectl rollout status deployment/kiali -n istio-system
```
- WE CAN CHECK THE kiali IS RUNNING OR NOT
```
kubectl -n istio-system get svc kiali
```
- TO START KIALI DASBOARD
```
istioctl dashboard kiali
```















