# Kubernetes (k8s)

Continainer orchestration engine.
It is a open source container orchestration engine developed by Google.
Currently maintained by Cloud Native Computing Foundation.
```
CLI	|						|----->  Worker node 1
API	|------>	k8s master	|----->  Worker node 2
GUI	|						|----->  Worker node 3
```

k8s master receives commands thourgh API, CLI, GUI and executes on worker nodes

### What is record of intent ?
Support you want to run a application in k8s, you will specify to k8s master node via cli, api, gui that <br> 
you want to deploy the application on k8s cluster. Now, k8s using different algorithm runs the application <br> 
on any available nodes in k8s. What k8s master will do is, it will launch the application on any available node <br>
and run it. it keeps on monitoring the application and node and if the worker node is unhealthy or app container is unhealthy<br>
then it moves the application to run on another node.
Here you just specified that you want to deploy app, k8s handles the rest where to deploy, scaling, migration etc.
This is known as record of intent.
It basically means whatever you want k8s to run, it will make sure your specified application is always running.
In k8s terms it is known as <b>K8s Objects</b>


## k8s commands

### Get nodes
```
kubectl get nodes

NAME                    STATUS   ROLES    AGE     VERSION
k8s-worker-node-3bw3x   Ready    <none>   9m38s   v1.18.6
k8s-worker-node-3bw3y   Ready    <none>   9m34s   v1.18.6
```

### Run containers
```
kubectl run nginx --image=nginx
```

### Get pods
```
kubectl get pods
```

# Installation
Can use k8s using different methods
1. Use managed k8s service (recommended)
2. use minikube (For study and practice purpose)
3. Install and configure k8s manually hard way

## Things to configure while working with k8s in hard way
1. kubectl = CLI for running user commands against cluster
2. k8s master node = k8s cluster by itself
3. k8s worker node = k8s worker node where jobs are scheduled.

## Things to configure while working with k8s in managed service
Just download k8s kubectl cli in and configure it
that's it
Master node and worker node is handled by managed service provider.

# Demo is done on managed k8s service on digital ocean because in DO k8s is free and you only pay for the worker node that's it. Other service providers like AWS, GCP they charge you for the master node and cluster as well.

## For demo part please setup k8s on DO

1. Once your k8s cluster is setup in DO
2. Download and install kubectl in your computer.
3. Download the config file and save it in ~/.kube folder [If .kube directory does not exists, please create if yourself]
4. If you do not want to pass the config file everytime you run kubectl command you can rename the config file which is in yaml format to just "config" eg. 
```
mv [XXXX.yaml] config
```

## Test if you k8s cluster is ready or not
Issue following commands:
```
kubectl get nodes

NAME                    STATUS   ROLES    AGE     VERSION
k8s-worker-node-3bw3x   Ready    <none>   9m38s   v1.18.6
k8s-worker-node-3bw3y   Ready    <none>   9m34s   v1.18.6
```

## k8s Objects
K8s Objects is basically a record of intent that you pass on to the k8s cluster.
When you create an Object in k8s, it will continuously work to make sure that object exists in cluster.