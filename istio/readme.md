# Install istio on Kubernetes cluster.
- Kubernetes cluster is already setup.
- Link is below
-  https://istio.io/latest/docs/setup/getting-started/#download
- 1 Download the installation file for your OS, or download and extract the latest release automatically (Linux or macOS):
curl -L https://istio.io/downloadIstio | sh -

- 2 Move to the Istio package directory. For example, if the package is istio-1.13.3:
cd istio-1.13.3

- 3 Add the istioctl client to your path (Linux or macOS):
export PATH=$PWD/bin:$PATH

# Install Istio
- 1 istioctl install --set profile=demo -y

✔ Istio core installed
✔ Istiod installed
✔ Egress gateways installed
✔ Ingress gateways installed
✔ Installation complete

- 2 Add a namespace label to instruct Istio to automatically inject Envoy sidecar proxies when you deploy your application later:

kubectl label namespace default istio-injection=enabled
namespace/default labeled

# Deploy the sample application
- 1 Deploy the Bookinfo sample application:
- kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
service/details created
serviceaccount/bookinfo-details created
deployment.apps/details-v1 created
service/ratings created
serviceaccount/bookinfo-ratings created
deployment.apps/ratings-v1 created
service/reviews created
serviceaccount/bookinfo-reviews created
deployment.apps/reviews-v1 created
deployment.apps/reviews-v2 created
deployment.apps/reviews-v3 created
service/productpage created
serviceaccount/bookinfo-productpage created
deployment.apps/productpage-v1 created

- 2 The application will start. As each pod becomes ready, the Istio sidecar will be deployed along with it.

- kubectl get services

NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
details       ClusterIP   10.0.0.212      <none>        9080/TCP   29s
kubernetes    ClusterIP   10.0.0.1        <none>        443/TCP    25m
productpage   ClusterIP   10.0.0.57       <none>        9080/TCP   28s
ratings       ClusterIP   10.0.0.33       <none>        9080/TCP   29s
reviews       ClusterIP   10.0.0.28       <none>        9080/TCP   29s

- 3 Verify everything is working correctly up to this point. Run this command to see if the app is running inside the cluster and serving HTML pages by checking for the page title in the response:

kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"

- 4 identify which version of Istio was installed
- this will give you all deployed objects belongs to Istio CRs in all namespaces
```
kubectl api-resources | grep -i istio | awk '{print $4}' | while read cr; do
    kubectl get $(echo $cr | tr '[:upper:]' '[:lower:]') --all-namespaces
done
```

- This command can use to make sure that any new pods that are created in dev namespace will automatically have an Istio sidecar proxy added to them.
```
kubectl label namespace dev istio-injection=enabled
```
-  a valid routing API version of Istio: networking.isito/v1alpha3

# This command use to validate the istio configuration
```
istioctl analyze
```














