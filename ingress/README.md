#  -----INGRESS----
kubectl get deployments --all-namespaces
kubectl get ingress --all-namespaces
kubectl describe ingress <ingerresName> -n <nameSpace>
kubectl get ingress <ingressName> -n <nameSpace> -o yaml > definationFile.yaml
kubectl delete ingress <ingressName> -n <nameSpace>
kubectl apply -f definationFile.yaml
kubectl get deployments,svc -n <nameSpace>
***
Ingress objelerinin listelenmesi

```
$ kubectl get ingress
```
***
Ingress objelerinin silinmesi

```
$ kubectl delete ingress "statefulset_ingress"

Ör: kubectl delete ingress my-ingress
```
***