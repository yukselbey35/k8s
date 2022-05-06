# Add the Kasten Helm 10 repository
```
helm repo add kasten https://charts.kasten.io/
```
# Install MySQL and Create a Demo Database
- To experiment with backup and recovery of a cloud-native application, we will install MySQL and create a database in this step.

- First, install MySQL using the following commands:
```
kubectl create namespace mysql
helm install mysql bitnami/mysql --namespace=mysql
```
# To ensure that MySQL is running, check the pod status to make sure they are all in the Running state:
```
watch -n 2 "kubectl -n mysql get pods"
```

- Once all pods have a Running status, and Ready is 1/1, hit CTRL + C to exit watch and then run the following commands to create a local database.

# Install Kasten K10
```
kubectl create namespace kasten-io; \
  helm install k10 kasten/k10 --namespace=kasten-io
```

- To ensure that Kasten K10 is running, check the pod status to make sure they are all in the Running state:
```
watch -n 2 "kubectl -n kasten-io get pods"
```
- Once all pods have a Running status, hit CTRL + C to exit watch.

- Configure the Local Storage System
- Once K10 is running, use the following commands to configure the local storage system.

```
kubectl annotate volumesnapshotclass csi-hostpath-snapclass k10.kasten.io/is-snapshot-class=true
```
