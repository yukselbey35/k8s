# Create Traffice Into Service Mesh
- The gateway configures our service mesh to accept traffic from outside the cluster.

- We can use example file to install a demo
- Use and example yaml to install on minikube

- then validate with
```
istioctl analyze 
```
-  To find which IP our local minikube cluster reachable 
```
minikube ip
```
- Result: 192.168..54

- Export this IP to use more efficently
```
export INGRESS_HOST=$(minikube ip)
```























