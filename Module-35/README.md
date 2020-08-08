# Node Port

Port exposed on the node.

Node port connects to service IP, which forwards to the respective port

You can you any of the services Types:
1. ClusterIP

A service of type ClusterIP exposes a service on an internal IP in the cluster, which makes the service only reachable from within the cluster. This is the default value if no type is specified.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx-app
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx
        image: nginx:1.13.12
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx-app
  name: nginx-svc
  namespace: default
spec:
  type: ClusterIP  # use ClusterIP as type here
  ports:
    - port: 80
  selector:
    app: nginx-app
```

2. NodePort

A service of type NodePort is a ClusterIP service with an additional capability: it is reachable at the IP address of the node as well as at the assigned cluster IP on the services network. The way this is accomplished is pretty straightforward: when Kubernetes creates a NodePort service kube-proxy allocates a port in the range 30000–32767 and opens this port on every node (thus the name “NodePort”). Connections to this port are forwarded to the service’s cluster IP. If we create the service above and run kubectl get svc <your-service>, we can see the NodePort that has been allocated for it.

In above code instead of ClusterIP add NodePort
```
spec:
   type: NodePort
   ports:
     - port: 80
```

3. Loadbalancer 

The LoadBalancer type is the simplest approach, which is created by specifying type as LoadBalancer.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx-app
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx
        image: nginx:1.13.12
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx-app
  name: nginx-svc
  namespace: default
spec:
  type: LoadBalancer  # use LoadBalancer as type here
  ports:
    - port: 80
  selector:
    app: nginx-app
```

### LoadBalancer vs. Ingress

As presented in the previous section, only the service type LoadBalancer enables access from outside the cluster. However this approach has its own limitation. You cannot configure a LoadBalancer to terminate HTTPS traffic, virtual hosts or path-based routing. In Kubernetes 1.2 a separate resource called Ingress is introduced for this purpose.

### Why an Ingress$
LoadBalancer services are all about extending a service to support external clients. By contrast an Ingress is a a separate resource that configures a LoadBalancer in a more flexible way. The Ingress API supports TLS termination, virtual hosts, and path-based routing. It can easily set up a load balancer to handle multiple backend services. In addition routing traffic is realised in a different way. In the case of the LoadBalancer service, the traffic entering through the external load balancer is forwarded to the kube-proxy that in turn forwards the traffic to the selected pods. In contrast, the Ingress LoadBalancer forwards the traffic straight to the selected pods which is more efficient.

Typically a service of type LoadBalancer costs at least 40$ per month. This means if your applications needs 10 of them you already pay 400$ per month just for load balancing.

### How to use the ingress?
In the cluster, a nginx-ingress controller has been deployed for you as an LoadBalancer and also registered the DNS record. Depending on how your cluster is defined, the DNS registration is performed under following conventions:
