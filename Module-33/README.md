# k8s Configmap

To store centrally storing data such as configuration in k8s

Suppose parameter has a lot of informatio and changes based on environment then we use ConfigMap

## Creating config map
kubectl create configmap NAME DATASOUECE

Can use DATA sources below:
name
directory
literal value

```
kubectl get configmap -o yaml
```
Create configmap
```
kubectl create configmap dev-config --from-lliteral=app.mem-2068
```

Create configmap from file
```
kubectl create configmap dev-properties --from-file=dev.properties
```

See configuration of configmap
```
kubectl describe configmap dev-config    
Name:         dev-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
app.mem:
----
2068
Events:  <none>
```

## Mount configmap in pod
We use volume mount to map the configmap, first create configmap
```
apiVersion: v1
kind: Pod
metadata:
  name: config-pod
spec:
  containers:
  - name: config-pod
    image: nginx:alpine
    ports:
      - containerPort: 80
    volumeMounts:
      - name: config-pod
        mountPath: /etc/config
  volumes:
  - name: config-pod
    configMap:
      name: dev-properties
```