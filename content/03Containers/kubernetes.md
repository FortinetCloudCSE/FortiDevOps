---
title: "Kubernetes Fundamentals"
linkTitle: "Kubernetes Fundamentals"
weight: 3
---

## Introduction
Kubernetes, often abbreviated as K8s, is an open-source container orchestration platform. It automates the deployment, scaling, and management of containerized applications. Originally developed by Google, Kubernetes has gained widespread adoption in managing containerized workloads and services.

#### Basic Kubernetes Components:

- **Pods**: The smallest deployable unit in Kubernetes, consisting of one or more containers that share storage and networking. They're scheduled together on the same node. It is an abstraction over the container run time, Docker for

- **Nodes:** These are the individual machines (physical or virtual) in a Kubernetes cluster where containers are deployed. Each node has services to run applications in the form of pods.

- **Clusters:** A set of nodes that work together. Clusters are the foundation of Kubernetes; they consist of at least one master node and multiple worker nodes.

- **Master Node:** Controls the Kubernetes cluster and manages its workload. It coordinates all activities in the cluster such as scheduling, scaling, and updating applications.

- **Kubelet:** An agent running on each node that communicates with the master node and manages the pods and containers on the node.

- **Kube-proxy:** Maintains network rules on nodes. It enables communication between different pods and services within the cluster.

- **Controller Manager:** Manages controller processes that regulate the state of the cluster, such as deployments, replica sets, and stateful sets.

- **Scheduler:** Assigns pods to nodes based on resource requirements and other constraints.

- **Etcd:** A distributed key-value store that stores the cluster's configuration data, representing the state of the cluster at any given time.

These components work together to ensure that applications run efficiently, are scalable, and can easily be managed in a containerized environment.


## Task 0: Set up you testing environment

#### MiniKube
Minikube is a tool that allows you to run a single-node Kubernetes cluster locally on your computer. It's designed to enable developers to learn and experiment with Kubernetes or to develop applications locally before deploying them to a larger Kubernetes cluster.

##### Install Minikube

Follow the steps in the here to install and start Minikube: [https://minikube.sigs.k8s.io/docs/start/](https://minikube.sigs.k8s.io/docs/start/)


#### kubectl

`kubectl` is the command-line interface (CLI) used to interact with Kubernetes clusters. It allows users to execute commands against Kubernetes clusters to deploy applications, manage and inspect cluster resources, and perform various administrative tasks.

##### Install kubectl
kubectl will be configured automatically to authenticate to Minikube during the minikube installation process (check `~/.kube/config`). However, you might still need to install `kubectl` itself. To install `kubectl` Follow the steps here:[https://kubernetes.io/docs/tasks/tools/]( https://kubernetes.io/docs/tasks/tools/)

#### Clone examples repository

Once you have your environment ready, clone the following repository

```bash
git clone https://github.com/Ahmed-AG/k8s-quick-start-tutorial.git
```

### Next
[Task 1: Basic kubectl commands](/read/kubernetes-crash-course-task1.html)


## Task 1: Basic kubectl commands

Let us explore our Minikube environment:
Run the help to learn about the basic commands:

```bash
kubectl -h
```
```
$ kubectl -h
kubectl controls the Kubernetes cluster manager.

 Find more information at: https://kubernetes.io/docs/reference/kubectl/

Basic Commands (Beginner):
  create          Create a resource from a file or from stdin
  expose          Take a replication controller, service, deployment or pod and expose it as a new Kubernetes service
  run             Run a particular image on the cluster
  set             Set specific features on objects

Basic Commands (Intermediate):
  explain         Get documentation for a resource
  get             Display one or many resources
  edit            Edit a resource on the server
  delete          Delete resources by file names, stdin, resources and names, or by resources and label selector
...
```
Notice that we can create, delete, get and describe resources. We will later work with the following resources:
- Pods: An abstraction over a container run time such as Docker.
- Deployments: we don't directly work with pods. Instead, we create deployments that could have one or multiple pods performing the same task.
- Services: Provide way to access a set of deployments (and therefore pods), typically through an endpoint (IP Addresses and Names).
- ConfigMaps: We will use ConfigmMps to store information that is needed accross deployments.
- Secrets: Very similar to ConfigMaps except it is meant for secret values. We will use Secrets to store Database credentials.


#### Create a deployment
The following command will instruct K8s to create a deployment and a pod with nginx docker container image `nginx`. It will set a lot of defaults as well

```bash
kubectl create deployment nginx --image=nginx
```
Let us explore the deployment we created. Run the following command:

```bash
kubectl get deployment
```

```bash
$kubectl get deployment
NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
nginx                               1/1     1            1           14s
```

We can also explore more:
```bash
kubectl get pods
```
```bash
$ kubectl get pods
NAME                                                READY   STATUS    RESTARTS   AGE
nginx-7854ff8877-6wq4n                              1/1     Running   0          3m23s
```

We can use the `-o wide` option to show more information such as the IP addresses:

```bash
kubectl get pods -o wide
```

```
$ kubectl get pods -o wide
NAME                                                READY   STATUS    RESTARTS   AGE     IP            NODE       NOMINATED NODE   READINESS GATES
nginx-7854ff8877-6wq4n                              1/1     Running   0          5m11s   10.244.0.31   minikube   <none>           <none>
```

We can also `describe` resources using the command `kubectl describe pods <POD-NAME>`:

```bash
kubectl describe pods nginx-7854ff8877-6wq4n
```
```
$ kubectl describe pods nginx-7854ff8877-6wq4n
Name:             nginx-7854ff8877-6wq4n
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.59.100
Start Time:       Fri, 22 Dec 2023 19:34:20 -0600
Labels:           app=nginx
                  pod-template-hash=7854ff8877
Annotations:      <none>
Status:           Running
IP:               10.244.0.31
IPs:
  IP:           10.244.0.31
Controlled By:  ReplicaSet/nginx-7854ff8877
Containers:
  nginx:
    Container ID:   docker://455c96e8e59fb29fd3aabe439de64c9e0b1b1de0dc691fd0389afbdab382a025
    Image:          nginx
...
```

##### Exploring the pods
So far we have created one `deployment` and one `pod`. Use the following to execute commands inside a specific pod (Container): `kubectl exec  <pod-name> -- <Shell Command>` 

```bash
kubectl exec nginx-7854ff8877-6wq4n -- uname -a
```
```
$ kubectl exec nginx-7854ff8877-6wq4n -- uname -a
Linux nginx-7854ff8877-6wq4n 5.10.57 ###1 SMP Tue Nov 7 06:51:54 UTC 2023 x86_64 GNU/Linux
```

We can also get a shell on a container:
```bash
kubectl exec -ti nginx-7854ff8877-6wq4n -- /bin/bash
```
```
$ kubectl exec -ti nginx-7854ff8877-6wq4n -- /bin/bash
root@nginx-7854ff8877-6wq4n:/# uname -a
Linux nginx-7854ff8877-6wq4n 5.10.57 ###1 SMP Tue Nov 7 06:51:54 UTC 2023 x86_64 GNU/Linux
root@nginx-7854ff8877-6wq4n:/#
```

Finally, we can read logs from a pod using `kubectl logs <pod-name>`
```bash
kubectl logs nginx-7854ff8877-6wq4n
```
```
$ kubectl logs nginx-7854ff8877-6wq4n
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2023/12/23 01:34:33 [notice] 1###1: using the "epoll" event method
2023/12/23 01:34:33 [notice] 1###1: nginx/1.25.3
2023/12/23 01:34:33 [notice] 1###1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2023/12/23 01:34:33 [notice] 1###1: OS: Linux 5.10.57
2023/12/23 01:34:33 [notice] 1###1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2023/12/23 01:34:33 [notice] 1###1: start worker processes
2023/12/23 01:34:33 [notice] 1###1: start worker process 28
2023/12/23 01:34:33 [notice] 1###1: start worker process 29
```


## Task 2: Building a sample application

Kubernetes support building infrastructure using configuration files. K8s configuration files are YAML or JSON files used to define and manage Kubernetes resources, such as pods, deployments, services, etc. These files contain specifications that describe the desired state of the resources you want to create or modify within a Kubernetes cluster.

Here are some key components and sections commonly found in Kubernetes configuration files:
- API Version: Specifies the version of the Kubernetes API that the object uses.
- Kind: Defines the type of Kubernetes resource being created, such as Pod, Deployment, Service, etc.
- Metadata: Contains information like the name, labels, and annotations for the resource.
- Spec: Describes the desired state of the resource. This section varies based on the resource type. For example:
  - Pods: Contains specifications for containers, volumes, and other settings.
  - Deployments: Includes information about replicas, pod templates, update strategies, etc.
  - Services: Defines how the service should be exposed, its ports, selectors, etc.
- Selectors: Often used in conjunction with services or controllers to define how resources are associated or targeted.
- Annotations: Additional information or metadata that can be added to the resource for various purposes like documentation, tooling, etc.

We have three configuration files being used for this tutorial. You can find them [here](https://github.com/Ahmed-AG/k8s-quick-start-tutorial/blob/main/example1-mongoApp/)

### Build a sample application
In this example, we will build a sample application that consists of the following:
1. A MongoDB as the `Backend`. Accessible only for the Frontend and consists of one Pod
2. A Mongo-express as the `Frontend`. Accessible from the outside and consists of one Pod

To do that, we will use three different configuration files as follows:
- [backend-mongo-db.yaml](https://github.com/Ahmed-AG/k8s-quick-start-tutorial/blob/main/example1-mongoApp/backend-mongo-db.yaml): Will create a Deployment and a Service for the backend
- [frontend-mongo-express.yaml](https://github.com/Ahmed-AG/k8s-quick-start-tutorial/blob/main/example1-mongoApp/frontend-mongo-express.yaml): Will create a Deployment and a Service for the frontend
- [mongodb-configmap.yaml](https://github.com/Ahmed-AG/k8s-quick-start-tutorial/blob/main/example1-mongoApp/mongodb-configmap.yaml): Will create ConfigMap that will have the backend's name
- Finally, we will create `sectret` that has the Database user name and password. We will do that though the command line

We will begin with creating the secret.

### Creating the secret:
`Secrets` are a way to securely store sensitive information, such as passwords, tokens, or keys. They're designed to help manage sensitive data access within Kubernetes deployments. Secrets provide a layer of abstraction and security by encoding and storing sensitive information separately from pod definitions or application code.

Kubernetes secrets can be used by referencing them in pods or containers, allowing applications to access sensitive information without exposing it directly within the code or configuration files. Secrets are stored within the cluster `etcd` and can be accessed by authorized entities.

Both the backend and the frontend will need to access the secret we will create. On the backend we will need to set the username and password of the Database. And on the front end we will need to configure the right username and password to be used.

So, we will create two secrets:
- mongo-username
- mongo-password
Because we want to be able to do this in a pipeline, we chose to use the `kubectl` to create the secrets. Run the following (you can choose your password):

```bash
kubectl create secret generic mongodb-secret --from-literal=mongo-username=mongouser --from-literal=mongo-password=mongopass
```
To verify that the above was successful:
```bash
kubectl get secrets
```
```
$ kubectl get secrets
NAME             TYPE     DATA   AGE
mongodb-secret   Opaque   2      5d
```
You can also run:
```bash
kubectl describe secrets mongodb-secret
```
```
$ kubectl describe secrets mongodb-secret
Name:         mongodb-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
mongo-password:  9 bytes
mongo-username:  9 bytes
```



## Task 3: Create the Backend

The full configuration file is [here](https://github.com/Ahmed-AG/k8s-quick-start-tutorial/blob/main/example1-mongoApp/backend-mongo-db.yaml). But let us dissect it.
This file will create two resources:
- A `Deployment` for the backend
- A `Service` for that backend

#### Backend Deployment

First we need to configure the resource Type and some Metadata
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-mongodb-deployment
  labels:
    app: backend-mongodb
```

The above sets a name for this deployment and also a label that will be used to reference this deployment later on.
Next,  we need to set some specifications:
```
spec:
  replicas: 1
  selector:
    matchLabels:
      app: backend-mongodb
  template:
    metadata:
      labels:
        app: backend-mongodb
```
In the above code we set the replicas to `1` because we need one Pod. and we also set the selector to select the label `app: backend-mongodb`

Next, we need to set the container specification. We have an `Container Image` that is already configured with MongoDB. We will set that, and we will also set the default port.
```
    spec:
      containers:
      - name: backend-mongodb
        image: mongo
        ports:
        - containerPort: 27017
```
The next step is for us to configure the username and password for the Database. We can do that by setting the environment variables `MONGO_INITDB_ROOT_USERNAME` and `MONGO_INITDB_ROOT_PASSWORD` inside the container. The values of those are being referenced from the `mongodb-secret` secret we set earlier.

```
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-passwor
```
#### Creating the service
Finally, we are going to create service and tie it to the deployment we just created:
```
apiVersion: v1
kind: Service
metadata:
  name: backend-mongodb
spec:
  selector:
    app: backend-mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
```

Notice that we used the `app: backend-mongodb` as a selector and we exposed the same port `27017`. Notice also that we don't have anything that dictates wether we should expose access to this service externally. The default is that we will not.

#### Apply the Backend

```bash
kubectl apply -f example1-mogoApp/backend-mongo-db.yaml
```


## Task 4: Create the Frontend

#### ConfigMap
A ConfigMap in Kubernetes is an object used to store configuration data in key-value pairs. It provides a way to decouple configuration artifacts from container images, allowing you to manage configurations separately from the application code.

ConfigMaps are commonly used to store non-sensitive, configuration-specific data, such as environment variables, command-line arguments, configuration files, or other settings required by applications running in Kubernetes pods.

We need to configure the mongodb-configmap to store the `database_url: backend-mongodb` value for reference in the Frontend. [Full file is here](https://github.com/Ahmed-AG/k8s-quick-start-tutorial/tree/main/example1-mongoApp):

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongodb-configmap
data:
  database_url: backend-mongodb
```

#### Apply the ConfigMap
```bash
kubectl apply -f example1-mogoApp/mongodb-configmap.yaml
```

#### Frontend Deployment:
First, we need to set up the `Kind` as `Deployment`, the name and the same basic Replicas and Selector as before

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-mongo-express-deployment
  labels:
    app: frontend-mongo-express
spec:
  replicas: 1
  selector:
    matchLabels:
      app: frontend-mongo-express
  template:
    metadata:
      labels:
        app: frontend-mongo-express
```
Then, we will set a container image that is preconfigured with MongoExpress: `mongo-express`. We will also set the default port. And we will configure `ME_CONFIG_MONGODB_ADMINUSERNAME` and `ME_CONFIG_MONGODB_ADMINPASSWORD` environment variables
```
    spec:
      containers:
      - name: mongo-express
        image: mongo-express
        ports:
        - containerPort: 8081
        env:
        - name: ME_CONFIG_MONGODB_ADMINUSERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-username
        - name: ME_CONFIG_MONGODB_ADMINPASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-password
```
The last thing we need to add in the deployment is setting up the Database server name. We will do that using the ConfigMap data we set earlier
```
        - name: ME_CONFIG_MONGODB_SERVER
          valueFrom: 
            configMapKeyRef:
              name: mongodb-configmap
              key: database_url
```

#### Frontend Service:
The service for the frontend deployment is slightly different than the service do the backend deployment. In the frontend Service we will need to expose it to the internet:
```
apiVersion: v1
kind: Service
metadata:
  name: frontend-mongo-express
spec:
  selector:
    app: frontend-mongo-express
  type: LoadBalancer  
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: 30000
```
Notice that we set the `type` to `LoadBalancer`, which will expose this service externally. And we had to add `nodePort: 30000` which will set the port to be used externally.

#### Apply the Frontend
```bash
kubectl apply -f example1-mogoApp/frontend-mongo-express.yaml
```
Give it few minutes for all services to be created then move to the next step.

### Expose the Frontend using Minikube
```bash
minikube service frontend-mongo-express
```
The above command will expose `frontend-mongo-express` service and give it a public IP address

![Minikube exposing frontend-mongo-express](https://raw.githubusercontent.com/Ahmed-AG/k8s-quick-start-tutorial/main/images/screenshot-minikube-expose.png?raw=true)

You can access the frontend by pointing your browser to the IP address that was assigned. Notice that Mongo-express allows you to interact with the backend which is the MongoDB server (Service)

![Firefox-mongoexpress](https://raw.githubusercontent.com/Ahmed-AG/k8s-quick-start-tutorial/main/images/screenshot-firefox-mongoexpress.png){:width="80%"}

Use `admin` and `pass` to login to Mongo-Express.
Congratulations! Your Application is built!



## Task 5: Namespace

`Namespaces` are a way to divide cluster resources between multiple users, teams, or projects. They provide a scope for Kubernetes objects, such as pods, services, and replication controllers, within a cluster.

Namespaces are useful for organizing and isolating resources, allowing different teams or projects to operate independently within the same Kubernetes cluster without interfering with each other. They help in avoiding naming collisions and provide a level of resource isolation and security.

By default, Kubernetes has a 'default' namespace, but you can create multiple namespaces to logically segregate and manage your resources more effectively. Each namespace operates as a separate virtual cluster within the larger Kubernetes cluster, enabling teams to work in their dedicated space without conflicting with others.

To explore namespaces run the following:
```bash
kubectl get namespaces
```
```
$ kubectl get namespaces
NAME              STATUS   AGE
default           Active   17h
kube-node-lease   Active   17h
kube-public       Active   17h
kube-system       Active   17h
```
Notice that there is a `default` namespace. This is where we created all of our resources so far.
Run the following command to view all resources in the default namespace:

```bash
kubectl get all -n default
```

#### Blue/Green Deployment
There are many ways for us to use namespaces, we can specify the namespace inside YAML configuration file itself under `metadata`:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-mongodb-deployment
  namespace: my-awesome-namespace
  labels:
    app: backend-mongodb
```

Or we can pass the namespace as a parameter when we apply a configuration file: `kubectl apply -f <file.yaml> --namespace my-awesome-namespace`.
For our example, we will use Namespaces to deploy two versions of our application. a Blue one and a Green one. Assuming that we will use deployment piplines to deploy these, it makes sense for us to use the command line option.

Let us first clean up the previous deployments we have:
```bash
kubectl delete -f example1-mongoApp/backend-mongo-db.yaml
kubectl delete -f example1-mongoApp/mongodb-configmap.yaml
kubectl delete -f example1-mongoApp/frontend-mongo-express.yaml
kubectl delete secret mongodb-secret
```
Verify your work by running:
```bash
kubectl get all
```
Now that we have a clean slate, let us create our two namespaces:
```bash
kubectl create namespace blue
kubectl create namespace green
```
Let us deploy our `Blue` deployment first:
```bash
deployment=blue
kubectl create secret generic mongodb-secret --from-literal=mongo-username=mongouser --from-literal=mongo-password=mongopass --namespace $deployment
kubectl apply -f example1-mongoApp/backend-mongo-db.yaml --namespace $deployment
kubectl apply -f example1-mongoApp/mongodb-configmap.yaml --namespace $deployment
kubectl apply -f example1-mongoApp/frontend-mongo-express.yaml --namespace $deployment
```

Notice that we are using the `$deployment` variable to simulate what we would do in a deployment pipeline.
Let us check out our work:
```bash
kubectl get all
```
The above command should not show our resources. But the next one should:
```bash
kubectl get all -n blue
```

Let us expose that. If we run `minikube service frontend-mongo-express`, we should see the following error:
```bash
$ minikube service frontend-mongo-express

❌  Exiting due to SVC_NOT_FOUND: Service 'frontend-mongo-express' was not found in 'default' namespace.
You may select another namespace by using 'minikube service frontend-mongo-express -n <namespace>'. Or list out all the services using 'minikube service list'
```
So let us specify the namespace:
```bash
minikube service frontend-mongo-express -n blue
```
Great! We have our blue deployment up and running!
Let us create the green deployment:
```bash
deployment=green
kubectl create secret generic mongodb-secret --from-literal=mongo-username=mongouser --from-literal=mongo-password=mongopass --namespace $deployment
kubectl apply -f example1-mongoApp/backend-mongo-db.yaml --namespace $deployment
kubectl apply -f example1-mongoApp/mongodb-configmap.yaml --namespace $deployment
kubectl apply -f example1-mongoApp/frontend-mongo-express.yaml --namespace $deployment
sleep 60
minikube service frontend-mongo-express -n $deployment
```
OOPS! We need to change the port!
```
The Service "frontend-mongo-express" is invalid: spec.ports[0].nodePort: Invalid value: 30000: provided port is already allocated

❌  Exiting due to SVC_NOT_FOUND: Service 'frontend-mongo-express' was not found in 'green' namespace.
You may select another namespace by using 'minikube service frontend-mongo-express -n <namespace>'. Or list out all the services using 'minikube service list'
```
To fix that, we can create another version of `example1-mongoApp/frontend-mongo-express.yaml` with a different port. We have created one that has `nodePort: 3001` located in `example1-mongoApp/frontend-mongo-express-green.yaml`. Let us try again using the green version:
```bash
deployment=green
kubectl create secret generic mongodb-secret --from-literal=mongo-username=mongouser --from-literal=mongo-password=mongopass --namespace $deployment
kubectl apply -f example1-mongoApp/backend-mongo-db.yaml --namespace $deployment
kubectl apply -f example1-mongoApp/mongodb-configmap.yaml --namespace $deployment
kubectl apply -f example1-mongoApp/frontend-mongo-express-green.yaml --namespace $deployment
sleep 60
minikube service frontend-mongo-express -n $deployment
```
To verify your work, you can use your browser to view the application. You can get the DNS names or IP addresses but looking at the services:
```bash
kubectl get services -n blue
kubectl get services -n green
```
```
$ kubectl get services -n blue
NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
backend-mongodb          ClusterIP      10.104.133.115   <none>        27017/TCP        19h
frontend-mongo-express   LoadBalancer   10.102.67.181    <pending>     8081:30000/TCP   19h
$ kubectl get services -n green
NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
backend-mongodb          ClusterIP      10.96.81.254     <none>        27017/TCP        19h
frontend-mongo-express   LoadBalancer   10.111.153.234   <pending>     8081:30001/TCP   8m16s
```

Nice! Now you have two versions running on different ports! But that is not the best way to do that! A better way would be to use a placeholder with `sed` to dynamically set the `nodePort`. The other option is to use [HELM Charts](https://helm.sh/docs/topics/charts/). We will cover HELM in a later task.

#### Cleanup
```bash
deployment=blue
kubectl create secret generic mongodb-secret --from-literal=mongo-username=mongouser --from-literal=mongo-password=mongopass --namespace $deployment
kubectl delete -f example1-mongoApp/backend-mongo-db.yaml --namespace $deployment
kubectl delete -f example1-mongoApp/mongodb-configmap.yaml --namespace $deployment
kubectl delete -f example1-mongoApp/frontend-mongo-express.yaml --namespace $deployment
```

```bash
deployment=green
kubectl create secret generic mongodb-secret --from-literal=mongo-username=mongouser --from-literal=mongo-password=mongopass --namespace $deployment
kubectl delete -f example1-mongoApp/backend-mongo-db.yaml --namespace $deployment
kubectl delete -f example1-mongoApp/mongodb-configmap.yaml --namespace $deployment
kubectl delete -f example1-mongoApp/frontend-mongo-express.yaml --namespace $deployment
```

## Task 6: Network Policies

So far we have created two different projects, we have created a project in a `blue` namespace and another in the `green` namespace. What if we needed to control network traffic between services or pods?
Let us do the following

Make sure you have two deployments `blue` and `green`
```bash
DEPLOYMENT=blue
kubectl create secret generic mongodb-secret --from-literal=mongo-username=mongouser --from-literal=mongo-password=mongopass --namespace $DEPLOYMENT
kubectl apply -f example1-mongoApp/backend-mongo-db.yaml --namespace $DEPLOYMENT
kubectl apply -f example1-mongoApp/mongodb-configmap.yaml --namespace $DEPLOYMENT
kubectl apply -f example1-mongoApp/frontend-mongo-express.yaml --namespace $DEPLOYMENT
sleep 60
minikube service frontend-mongo-express -n $DEPLOYMENT

DEPLOYMENT=green
kubectl create secret generic mongodb-secret --from-literal=mongo-username=mongouser --from-literal=mongo-password=mongopass --namespace $DEPLOYMENT
kubectl apply -f example1-mongoApp/backend-mongo-db.yaml --namespace $DEPLOYMENT
kubectl apply -f example1-mongoApp/mongodb-configmap.yaml --namespace $DEPLOYMENT
kubectl apply -f example1-mongoApp/frontend-mongo-express-green.yaml --namespace $DEPLOYMENT
sleep 60
minikube service frontend-mongo-express -n $DEPLOYMENT
```

The above will take a couple of minutes. To verify your work you can use your browser or run:

```bash
kubectl get pods -o wide -n blue
kubectl get pods -o wide -n green
```
```
$ kubectl get pods -o wide -n blue
kubectl get pods -o wide -n green
NAME                                                READY   STATUS    RESTARTS        AGE   IP              NODE       NOMINATED NODE   READINESS GATES
backend-mongodb-deployment-97bb5c688-pl6vm          1/1     Running   2 (148m ago)    8h    10.244.120.80   minikube   <none>           <none>
frontend-mongo-express-deployment-db9c8bd6b-rgw64   1/1     Running   53 (140m ago)   8h    10.244.120.79   minikube   <none>           <none>
NAME                                                READY   STATUS    RESTARTS       AGE   IP              NODE       NOMINATED NODE   READINESS GATES
backend-mongodb-deployment-97bb5c688-mzx96          1/1     Running   2 (148m ago)   8h    10.244.120.82   minikube   <none>           <none>
frontend-mongo-express-deployment-db9c8bd6b-hkfnv   1/1     Running   2 (148m ago)   8h    10.244.120.81   minikube   <none>           <none>
```

This should show you the IP addresses of all of your pods. Once our environment is built, let us access the front end on the `blue` deployment then try to ping the other pods:
```bash
kubectl exec -ti <POD NAME> -n blue -- /bin/bash
ifconfig
ping <Blue Frontend IP>
ping <Green Frontend IP>
ping <Green Backend IP>
```

```
$ kubectl exec -ti frontend-mongo-express-deployment-db9c8bd6b-rgw64 -n blue -- /bin/bash
```
```
frontend-mongo-express-deployment-db9c8bd6b-rgw64:/app# ifconfig
eth0      Link encap:Ethernet  HWaddr 7E:CA:CE:ED:C2:76  
          inet addr:10.244.120.79  Bcast:0.0.0.0  Mask:255.255.255.255
          UP BROADCAST RUNNING MULTICAST  MTU:1480  Metric:1
          RX packets:2431 errors:0 dropped:0 overruns:0 frame:0
          TX packets:3325 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:736401 (719.1 KiB)  TX bytes:275878 (269.4 KiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```
```
frontend-mongo-express-deployment-db9c8bd6b-rgw64:/app# ping 10.244.120.80 -c 3
PING 10.244.120.80 (10.244.120.80): 56 data bytes
64 bytes from 10.244.120.80: seq=0 ttl=63 time=0.124 ms
64 bytes from 10.244.120.80: seq=1 ttl=63 time=0.113 ms
64 bytes from 10.244.120.80: seq=2 ttl=63 time=0.140 ms

--- 10.244.120.80 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.113/0.125/0.140 ms
```
```
frontend-mongo-express-deployment-db9c8bd6b-rgw64:/app# ping 10.244.120.82 -c 3
PING 10.244.120.82 (10.244.120.82): 56 data bytes
64 bytes from 10.244.120.82: seq=0 ttl=63 time=0.252 ms
64 bytes from 10.244.120.82: seq=1 ttl=63 time=0.121 ms
64 bytes from 10.244.120.82: seq=2 ttl=63 time=0.086 ms

--- 10.244.120.82 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.086/0.153/0.252 ms
```
```
frontend-mongo-express-deployment-db9c8bd6b-rgw64:/app# ping 10.244.120.81 -c 3
PING 10.244.120.81 (10.244.120.81): 56 data bytes
64 bytes from 10.244.120.81: seq=0 ttl=63 time=0.623 ms
64 bytes from 10.244.120.81: seq=1 ttl=63 time=0.180 ms
64 bytes from 10.244.120.81: seq=2 ttl=63 time=0.115 ms

--- 10.244.120.81 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.115/0.306/0.623 ms
frontend-mongo-express-deployment-db9c8bd6b-rgw64:/app#
```

Notice that access is wide open!

### Apply Network Policies
There are multiple ways to apply network access in K8s. We will give one example that uses `apiVersion: networking.k8s.io/v1`. As a prerequisite, `Minikube` needs to be restarted with the `--cni calico`. To do so run the following:
```bash
minikube stop
minikube start --cni calico
```

This will enable network policies. Once Minikube is restarted we will apply [./example1-mongoApp/network-policy.yaml](https://github.com/Ahmed-AG/k8s-quick-start-tutorial/blob/main/example1-mongoApp/network-policy.yaml){:target="_blank"}. Let us examine the major sections:

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: network-policy
spec:
  podSelector:
    matchLabels:
      app: backend-mongodb
```

The above section dictates that we will create a `network-policy` resource that will be applied on pods that have the label: `backend-mongodb`. We can apply this network-policy on any Namespoace we want. Next, we need to set the rules. We want to only allow traffic from pods that have the label `app: frontend-mongo-express` and lives in the `green` namespace:
```
  policyTypes:
    - Ingress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              kubernetes.io/metadata.name: green
        - podSelector:
            matchLabels:
              app: frontend-mongo-express
```
Note that the actual file has many other commented out sections. feel free to explore those.
Let us apply this `network-policy` on the `green` namespace for this example:

```bash
kubectl apply -f example1-mongoApp/network-policy.yaml -n green
```

Try to access the Frontend pod on the blue namespace and try to ping the Backend on the blue, the Frontend on the green, and the Backend on the green

```bash
$ kubectl exec -ti frontend-mongo-express-deployment-db9c8bd6b-rgw64 -n blue -- /bin/bash
```
Ping the Backend on the blue Namespace:
```bash
frontend-mongo-express-deployment-db9c8bd6b-rgw64:/app# ping 10.244.120.80
PING 10.244.120.80 (10.244.120.80): 56 data bytes
64 bytes from 10.244.120.80: seq=0 ttl=63 time=0.327 ms
64 bytes from 10.244.120.80: seq=1 ttl=63 time=0.096 ms
^C
--- 10.244.120.80 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.096/0.211/0.327 ms
```
The Frontend on the green Namespace:
```bash
frontend-mongo-express-deployment-db9c8bd6b-rgw64:/app# ping 10.244.120.81
PING 10.244.120.81 (10.244.120.81): 56 data bytes
64 bytes from 10.244.120.81: seq=0 ttl=63 time=0.511 ms
64 bytes from 10.244.120.81: seq=1 ttl=63 time=0.102 ms
64 bytes from 10.244.120.81: seq=2 ttl=63 time=0.084 ms
^C
--- 10.244.120.81 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.084/0.232/0.511 ms
```
The Backend on the green Namespace:
```bash
frontend-mongo-express-deployment-db9c8bd6b-rgw64:/app# ping 10.244.120.82
PING 10.244.120.82 (10.244.120.82): 56 data bytes
^C
--- 10.244.120.82 ping statistics ---
4 packets transmitted, 0 packets received, 100% packet loss
```

Great! Backend on the green Namespace seems to be protected now. Let us see if the Frontend on the green namespace can access the Backend on the green namespace (Same Namespace):
```bash
kubectl exec -ti frontend-mongo-express-deployment-db9c8bd6b-hkfnv -n green -- /bin/bash
frontend-mongo-express-deployment-db9c8bd6b-hkfnv:/app# ping 10.244.120.82 -c 3
PING 10.244.120.82 (10.244.120.82): 56 data bytes
64 bytes from 10.244.120.82: seq=0 ttl=63 time=0.160 ms
64 bytes from 10.244.120.82: seq=1 ttl=63 time=0.067 ms
64 bytes from 10.244.120.82: seq=2 ttl=63 time=0.102 ms

--- 10.244.120.82 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.067/0.109/0.160 ms
frontend-mongo-express-deployment-db9c8bd6b-hkfnv:/app# exit
```

Sounds great! It seems that you have successfully protected the Backend on the green namespace deployment! Congratulation!

### References:
- [https://kubernetes.io/docs/concepts/services-networking/network-policies/](https://kubernetes.io/docs/concepts/services-networking/network-policies/) 
- A great resource for learning Kubernetes is this 4-hour [Youtube video](https://www.youtube.com/watch?v=X48VuDVv0do&t=3167s) by [TechWorld with Nana](https://www.youtube.com/@TechWorldwithNana)

