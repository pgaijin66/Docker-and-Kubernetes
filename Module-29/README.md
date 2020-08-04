# Kubernetes Objects
K8s Objects is basically a record of intent that you pass on to the k8s cluster.
When you create an Object in k8s, it will continuously work to make sure that object exists in cluster.

## Ways to create k8s objects
There are various ways you can create object in k8s
1. Using kubectl (Previously we create a k8s object called pod and ran a nginx webserver)
2. Using yaml configuration file shown below
```
apiVersion: v1
kind: Pod
metadata:
  name: mywebserver
spec:
  containers:
  - name: mywebserver
    image: nginx
```
Save the above code in a file called "pod.yaml"
The above yaml file does the same thing that we did using the kubectl command. It creates a k8s object pod which is defined by "kind: Pod". It provides name to the container and image to run the container from. Add metadata to that pod object.

Now to run this we call this applying configuration file to create a k8s object. We use following command
```
kubectl apply -f pod.yaml

pod/mywebserver created
```
Now it apples your configuration file to the k8s cluster. "-f" specifies that we are providing file as input. Its similar to running docker stack using
```
docker stack deploy -c DOCKERSTACKFILE
``` 

In production environment, always use the yaml file

### Advantage of configuration file
1. Version controlling
2. Change review process
3. Provides source of record on what is live within the kubernetes cluster
4. Easier to troubleshoot changes with version