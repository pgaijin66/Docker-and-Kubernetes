# k8s PODS

## What is pods in k8s?
In k8s a pod is a group of one or more applications containers and some shared resource for those containers.
For now to understand just replace your knowledge of container to pods. Pods means containers
until we get in depth into it.
A pod can have one or more containers(Recommended).
A pod can have multiple container and one shared volume.
A pod can have multiple container and multiple shared volume.

## Benefit of pods
Many applications that might have one or more containers which are tightly coupled and in one-to-one
replationship
Lets assume an application requires two container to work. In case one container fails in this architecutre, the application fails as we need both container are required
for the application to function properly
This application does not scale.
You need to have these container in same place and should be working.
To deal with such tightly coupled application you create container in a space called "POD".
Containers in a pod can share IP address and ports and can find each other via localhost.


## Comparing dockercli with k8s cli
Let's try to run a nginx container in docker and k8s to see how its done in both way.
```
docker run --name mywebserver nginx
```
would be
```
kubectl run mywebserver --image=nginx

pod/mywebserver created
```
This runs nginx container called mywebserver

Now to see the conatiners, in docker you used to do this
```
docker ps
```
in k8s we do this
```
kubectl get pods  

NAME          READY   STATUS    RESTARTS   AGE
mywebserver   1/1     Running   0          65s
```

### Exec into containers
To ssh into container, in docker we used to do this
```
docker exec -it mywebserver bash
```
In k8s we do this
```
kubectl exec --it mywebserver -- /bin/bash
root@mywebserver:/# 
```
The -- means that to specify kubectl that this is the end of command options, anyting after this is posititional parameters
Same applies for all linux

### Stop container
To delete the container in docker we do this
```
docker stop CONTAINER_NAME
docker rm CONTAINER_NAME
```
To do the same or remove pod in k8s we do this
```
kubectl delete pod POD_NAME
```

Even if we delete the pod, k8s keeps on creating same pods with different id. This is due to deployments.
Deployment makes sure that there is on instance of pod which is running 24/7. If you manually delete a pod, it will
automatically re-create pod again.
```
kubectl get deployments
```

NOTE: In later verson of k8s and kubectl when we create a pod it does not create a deployment. In older version of kubectl when you do kubectl run  command it adds "--generator=deploy,ent/apps.v1" to your command line and it creates deployments as well.
In newer version of kubectl it add "--generator=run-pod/v1" to kubectl run command so that i only creates on pod, not deployment