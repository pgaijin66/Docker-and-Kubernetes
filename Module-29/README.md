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

## Creating k8s object
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

apiVersion => Version of API that you want to use. Choosing which API you want to use depends on the Kind of k8s object you want to use. Since Pod is in version 1 or v1 API group so we put apiVersion 1. Path to see the available objects you go to /api/v1/

Kind => This specifies which kind of k8s Object we want to create. In Bove code we are creating a k8s object called Pod.

metadata => Uniquely identified k8s object (Pod) in given namespace.

spec => It specifies desired state of the k8s object which is Pod. You can find what specs in k8s objects from k8s documentation page. It provides you template required to create a pod.

containers => This specifies which container we want to run, and which image to use to run the container from.

### To see all list of api-resources
```
kubectl api-resources

NAME                               SHORTNAMES     APIGROUP                       NAMESPACED   KIND
bindings                                                                         true         Binding
componentstatuses                  cs                                            false        ComponentStatus
ersistentvolumes                  pv                                            false         PersistentVolume
pods                               po                                            true         Pod
podtemplates                                                                     true         PodTemplate
```

If you want to see in depth information about api resources we issue command "kubectl explain API_RESOURCE_NAME"
```
kubectl explain pod

KIND:     Pod
VERSION:  v1

DESCRIPTION:
     Pod is a collection of containers that can run on a host. This resource is
     created by clients and scheduled onto hosts.

FIELDS:
   apiVersion   <string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources

   kind <string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds

   metadata     <Object>
     Standard object's metadata. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

   spec <Object>
     Specification of the desired behavior of the pod. More info:
```

This will give you information about the api resource and also the url where you can go to read more about it. Some time the URL might be broken so needs to manually navigate through it.

Now let's create k8s object using yaml file. Add following code in pod2.yaml file
```
apiVersion: v1
kind: Pod
metadata:
  name: nginxwebserver
  labels:
    name: nginxwebserver
spec:
  containers:
  - name: myapp
    image: nginx
    resources:
      limits:
        memory: "128Mi"
        cpu: "500m"
```

Now run the yaml file using 
```
kubectl apply -f pod2.yaml
```

The stages of pod creation in k8s different some of them are:
1. ContainerCreating
2. Running
3. Pending
4. Terminating

If you do a kubectl get pods, you can see the status of k8s object creation step.