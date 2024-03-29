# My excursions with kubernetes
A simple project to deploy a REST API in a kubernetes cluster.
## Getting Started
These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Prerequisites
For the sake of brevity, the API will be deployed to a kubernetes cluster running on local machine. It is assumed that "minikube" and "kubectl" are already installed and a local cluster is running. If not, kindly refer to the official kubernetes documentation links mentioned below to quickly setup the environment:
* https://kubernetes.io/docs/tasks/tools/install-kubectl/
* https://kubernetes.io/docs/tasks/tools/install-minikube/

### Installation
Once the cluster is running, use the following commands to deploy the API.

Get the code from GitHub:
```
$ git clone https://github.com/pra-kum/kubernetes.git
$ cd kubernetes
```
Execute 'pods.yaml' file. It will fetch "rest-api-flask" image from Docker Hub and deploy two replicas of a pod running the API.

```
$ kubectl apply -f pods.yaml
```

Execute 'services.yaml' file. It will create a service which define the pods to be represented by this service.
The service will act as network endpoint for either other services or maybe external users to connect to (e.g. a web browser or a API testing tool like Postman)

```
$ kubectl apply -f services.yaml
```

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

The above output shows two pods(or two containers in this case) are running. Their names can be traced back to two replicasets and further to two deployments. The output also shows a service with the type of port it is using along with the port mapping.

### Testing the API

Get the CLUSTER IP of the running server as follows:
```
$ minikube ip
```
Note: The above IP is different from the IPs visible in the output of previous command `kubectl get all`

Perform a quick GET operation by accessing the API resource in a browser:

* http://\<CLUSTER IP\>:30080/items
  
or in a Terminal using curl:
``` 
$ curl http://<CLUSTER IP>:30080/items
```
Note: The port 30080 (defined in 'services.yaml') is required to access the service endpoint, which is further mapped to port 5000 on which the API in running inside the cluster (or EXPOSED by the docker container).

The result will be an empty list:
```
{
    "items": []
}
```

To further understand the operations supported by the API, please refer to the following link and access "section6" thenceforth:

* https://github.com/pra-kum/rest-api-sections


### Deletion

After testing the API, the following commands can be used to delete the deployment and service we created in this tutorial:

```
$ kubectl delete -f pods.yaml
$ kubectl delete -f services.yaml
```

Verify the deletion:
```
$ kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   11d
```

Since a cluster can consume a considerable amount of memory, you may need to stop the cluster when not using it as follows:

```
$ minikube stop
```

