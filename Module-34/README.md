# Service and endpoints (useful for loadbalancing and reverse proxing)

As we know pods are phemeral, their ip changes when we launch a pod.

If there are multiple pod communicating with one pod, for this is we specify a gateway

A k8s service can act as an abstraction which can provide a single ip address and DNS through which pods can be associated.

This layers of abstraction allows us to perform a lof of operations like load balancing, scaling of pods and other operations.

## Get services
```
kubectl get service

NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.245.0.1   <none>        443/TCP   5h28m
```
List pods

```
kubectl get pods -o wide  

NAME                               READY   STATUS                       RESTARTS   AGE     IP             NODE                  NOMINATED NODE   READINESS GATES
nginxserver1-768cf64cf4-2qr54      1/1     Running                      0          69s     10.244.0.73    k8s-sgp1-pool-3bk0a   <none>           <none>
nginxserver2-7978b4fdd6-6pdtp      1/1     Running                      0          63s     10.244.0.32    k8s-sgp1-pool-3bk0a   <none>           <none>
nginxserver3-58798df89d-mv6nq      1/1     Running                      0          57s     10.244.0.241   k8s-sgp1-pool-3bk0e   <none>           <none>
```

Assume we have 2 nginx pods which are running our website.

Lets imaging
Frontend
```
nginxserver1-768cf64cf4-2qr54      1/1     Running                      0          69s     10.244.0.73    k8s-sgp1-pool-3bk0a   <none>           <none>
```

Backend
```
nginxserver2-7978b4fdd6-6pdtp      1/1     Running                      0          63s     10.244.0.32    k8s-sgp1-pool-3bk0a   <none>           <none>
nginxserver3-58798df89d-mv6nq      1/1     Running                      0          57s     10.244.0.241   k8s-sgp1-pool-3bk0e   <none>           <none>
```

Frontend pod can communicate with both od the backend pods by default. But if backend pod fails we might get in trouble. 

WE need some kind of reverse proxy which will router our traffic to different backend node

## Endpoint

k8s endopoints tracks the ip address of the objects that service can send traffic to.

## bonus scale pod
```
kubectl scare --replicas=3 
```

## k8s service
1. create pods first
Can use cli or provide yaml file to create pods.


2. create service

Create service yaml file
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  ports:
  - port: 8080
    targetPort: 80

```

apply service
```
kubectl apply -f service.yaml
```

list services
```
kubectl get services

NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
kubernetes      ClusterIP   10.245.0.1       <none>        443/TCP    5h53m
nginx-service   ClusterIP   10.245.226.221   <none>        8080/TCP   6s
```

3. Create endpoints

First see the service endpoints
```
kubectl describe service nginx-service
Name:              nginx-service
Namespace:         default
Labels:            <none>
Annotations:       kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"nginx-service","namespace":"default"},"spec":{"ports":[{"port":80...
Selector:          <none>
Type:              ClusterIP
IP:                10.245.226.221
Port:              <unset>  8080/TCP
TargetPort:        80/TCP
Endpoints:         <none>
Session Affinity:  None
Events:            <none>
```
Now create endpoints and add both backend pods ip addresses
```
apiVersion: v1
kind: Endpoints
metadata:
  name: nginx-service
subsets:
  - addresses:
      - ip: 10.244.0.32
      - ip: 10.244.0.241
    ports:
      - port: 80
```

Apply endpoint 
```
kubectl apply -f endpoint.yaml
```

Now if you do see the service your end points are added
```
kubectl describe service nginx-service

Name:              nginx-service
Namespace:         default
Labels:            <none>
Annotations:       kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"nginx-service","namespace":"default"},"spec":{"ports":[{"port":80...
Selector:          <none>
Type:              ClusterIP
IP:                10.245.226.221
Port:              <unset>  8080/TCP
TargetPort:        80/TCP
Endpoints:         10.244.0.241:80,10.244.0.32:80
Session Affinity:  None
Events:            <none>
```

4. To test whether service is working or not with load balancing

Get the ip address of service
```
kubectl get service                   
NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
kubernetes      ClusterIP   10.245.0.1       <none>        443/TCP    6h3m
nginx-service   ClusterIP   10.245.226.221   <none>        8080/TCP   10m
```

Now exec into the frontend pod and try to do a curl on the service ip, it should return from any of the backend nginx servers.
```
# SSH into frontend pot
kubectl exec -it nginxserver1-768cf64cf4-2qr54 bash
root@nginxserver1-768cf64cf4-2qr54:/usr/local/nginx/html#

# curl on the service ip now instead of individual pod ip and port
root@nginxserver1-768cf64cf4-2qr54:/usr/local/nginx/html# curl 10.245.226.221:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
```

This is how service and endpoint works in k8s