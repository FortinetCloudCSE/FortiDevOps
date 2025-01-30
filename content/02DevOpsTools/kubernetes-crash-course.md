---
layout: reads
title:  "Kubernetes Crash Course"
date:   1-1-2024
author: "Ahmed Abugharbia"
author_link: "https://www.linkedin.com/in/ahmadabugharbieh/"
---
### Introduction
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

---
### Tasks:
- [Task 0:  Set up you testing environment](/read/kubernetes-crash-course-task0.html)
- [Task 1: Basic kubectl commands](/read/kubernetes-crash-course-task1.html)
- [Task 2: Building a sample application](/read/kubernetes-crash-course-task2.html)
- [Task 3: Create the Backend](/read/kubernetes-crash-course-task3.html)
- [Task 4: Create the Frontend](/read/kubernetes-crash-course-task4.html)
- [Task 5: Namespaces](/read/kubernetes-crash-course-task5.html)
- [Task 6: Network Policies](/read/kubernetes-crash-course-task6.html)

