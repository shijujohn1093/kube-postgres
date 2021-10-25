## create all kubernetes objects
kubectl create -f postgres-configmap_k8s.yml 
kubectl create -f postgres-pv_k8s.yml
kubectl create -f postgres-pvc_k8s.yml
kubectl create -f postgres-deployment_k8s.yml 
kubectl create -f postgres-svc_cip_k8s.yml
kubectl create -f postgres-svc_lb_k8s.yml
 
kubectl get all

$ kubectl get svc postgres
NAME       TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
postgres   NodePort   10.107.71.253   <none>        5432:31070/TCP   5m

## Delete all objects
kubectl delete service postgres
kubectl delete service postgresadminer
kubectl delete deployment postgres
kubectl delete configmap postgres-config
kubectl delete persistentvolumeclaim postgres-pv-claim
kubectl delete persistentvolume postgres-pv-volume




## Open a shell inside container, we can run sql over here
$ POD=`kubectl get pods -l app=postgres | grep Running | awk '{print $1}'`
$ kubectl exec -it $POD bash

psql -h localhost -U postgresadmin --password -p 30005 postgresdb

## Get into container inside port 
$ kubectl exec -it nginx -c sidecar -- /bin/sh