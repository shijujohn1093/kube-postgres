### We will be using config maps for storing PostgreSQL related information. Here, we are using the database, user and password in the config map which will be used by the PostgreSQL pod in the deployment template.



$ kubectl create -f postgres-configmap_k8s.yml 
configmap "postgres-config" created


### To save the data, we will be using Persistent volumes and persistent volume claim resource within Kubernetes to store the data on persistent storages. Here, we are using local directory/path as Persistent storage resource (/mnt/data)

$ kubectl create -f postgres-storage_k8s.yml 
persistentvolume "postgres-pv-volume" created
persistentvolumeclaim "postgres-pv-claim" created

### PostgreSQL manifest for deployment of PostgreSQL container uses PostgreSQL 10.4 image. It is using PostgreSQL configuration like username, password, database name from the configmap that we created earlier. It also mounts the volume created from the persistent volumes and claims to make PostgreSQL container’s data persists.

$ kubectl create -f postgres-deployment_k8s.yml 
deployment "postgres" created

### To access the deployment or container, we need to expose PostgreSQL service. Kubernetes provides different type of services like ClusterIP, NodePort and LoadBalancer. With ClusterIP we can access PostgreSQL service within Kubernetes. NodePort gives the ability to expose service endpoint on the Kubernetes nodes. For accessing PostgreSQL externally, we need to use a Load Balancer service type which exposes the service externally.
Note: May neet to change type from "NodePort" to  "LoadBalancer". and for internal service to access we may need to change port to ClusterIP

$ kubectl create -f postgres-service_k8s.yml
service "postgres" created


Reference : https://severalnines.com/database-blog/using-kubernetes-deploy-postgresql

# Connect to PostgreSQL

## For connecting PostgreSQL, we need to get the Node port from the service deployment.

$ kubectl get svc postgres
NAME       TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
postgres   NodePort   10.107.71.253   <none>        5432:31070/TCP   5m


# We need to use port 31070 to connect to PostgreSQL from machine/node present in kubernetes cluster with credentials given in the configmap earlier.
$ psql -h localhost -U postgresadmin1 --password -p 31070 postgresdb
Password for user postgresadmin1: 
psql (10.4)
Type "help" for help.
  
# For deletion of PostgreSQL resources, we need to use below commands.
$ kubectl delete service postgres 
$ kubectl delete deployment postgres
$ kubectl delete configmap postgres-config
$ kubectl delete persistentvolumeclaim postgres-pv-claim
$ kubectl delete persistentvolume postgres-pv-volume

