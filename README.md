# My excursions with kubernetes
A simple project to deploy a REST API in a kubernetes cluster.
## Getting Started
These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Prerequisites
For the sake of brevity, the API will be deployed to a kubernetes cluster running on local machine. It is assumed that "minikube" and "kubectl" are already installed and a local cluster is running. If not, kindly refer to the official kubernetes documnetation links mentioned below to quickly setup the environment:
https://kubernetes.io/docs/tasks/tools/install-kubectl/
https://kubernetes.io/docs/tasks/tools/install-minikube/

### Installation
Once the cluster is running, use the following commands to deploy the API:

Get the code from GitHub:
```
git clone https://github.com/pra-kum/kubernetes.git
cd kubernetes
```
Executing the pods.yaml file. It will fetch "rest-api-flask" image from Docker Hub and deploy two replicas of a pod running the API.
`kubectl apply -f pods.yaml`

Executing the services.yaml file. It will create a service that maps the pods to be represented by the service.
The service will act as network endpoint for either other services or maybe external users to connect to (eg browser or Postman tool)
`kubectl apply -f services.yaml`

The result can be verified as follows:
```
$ kubectl get all
NAME                                 READY   STATUS    RESTARTS   AGE
pod/rest-api-flask-cb89646d6-2t6xg   1/1     Running   1          10d
pod/rest-api-flask-cb89646d6-jhlhf   1/1     Running   1          10d

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP          11d
service/rest-api-flask   NodePort    10.106.96.187   <none>        5000:30080/TCP   10d

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/rest-api-flask   2/2     2            2           10d

NAME                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/rest-api-flask-cb89646d6   2         2         2       10d
```

