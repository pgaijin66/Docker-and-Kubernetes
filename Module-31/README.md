# Deployments

Deployments as we discussed before makes sure that k8s object specified is running at all times. Deployment provides replication features with the use of ReplicaSets, along with various additional capabilities like rolling changes, rolling back changes if required.

```
                        Deployment
                            |
                        ReplicaSet
                            |
                    -----------------
                    |       |       |
                    Pod1    Pod2    Pod3
```

### Benefits
##### Easy rolling out updates or changes to application, uses different deployment methods like canary so that there is 0 downtime.

In this what deployment does, it already has old application and replicaset running on server, now if you have new version of app which is tested and ready to go live into production, what it does is, it creates new replicaset first, creates new pod which run new version of app and deletes the old replicaset and old pod.

Deployment ensures that while updating or rolling out change at least 25% of the desired number of pods are up and 25% or max are unavailable

##### Easy rolling back of changes

With deployments you can rollback changes as well in the same manner as described above.

##### Maintains history

Deployment keep the history of version which has been made.

### Get deployments
```
kubectl get deployments
```

### Check rollout history associate with the deployment
This shows history of rollout changes and revision.
```
kubectl rollout history deployment.v1.apps/[DEPLOYMENT_NAME]
```
## Create a deployment

Copy following code and paste it into deployment.yaml file
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

Now lets apply that deployment using
```
kubectl apply -f deployment.yaml
```

### List pods
```
kubectl get pods

kubectl get pods                
NAME                                READY   STATUS    RESTARTS   AGE
mywebserver                         1/1     Running   0          81m
nginx-deployment-6b474476c4-2kh92   1/1     Running   0          101s
nginx-deployment-6b474476c4-w8btm   1/1     Running   0          101s
nginx-deployment-6b474476c4-z4kvh   1/1     Running   0          101s
```

Now you can see these 3 pods created itself are managed from replicaset, and replicaset are inturn managed my deployments.

### Implementing rolling out changes

Once you have your deployment running, if you want to roll out new changes

In our previous deployment we ran nginx pods with version 1.14.2, suppose now we want to run nginx version 1.17.3 which is the latest one. How do you do it?

We update the deployment file and the nginx version on the deployment file. Make changes 
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.17.3
        ports:
        - containerPort: 80
```

Apply the changes
```
kubectl apply -f deploymentyaml
```
Once you run this command it will say deployments.apps/nginx-deployment has been configured. and if you see the replicaset now
```
kubectl get rs

NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-579fcbbb4    1         1         0       12s
nginx-deployment-6b474476c4   3         3         3       6m32s
```

There are two replicaset running.

After some time if you check replica set again
```
kubectl get rs

NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-579fcbbb4    3         3         3       117s
nginx-deployment-6b474476c4   0         0         0       8m17s
```

You can see that all the old pods were deleted and new pods were created under new replicaset. This new replica set has the latest version of nginx running. Deployments also maintains the replicaset in case you want to roll back.

To see how k8s did that you issue following command
```
kubectl descibe describe deployments nginx-deployment

Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Fri, 07 Aug 2020 12:46:14 +1000
Labels:                 app=nginx
Annotations:            deployment.kubernetes.io/revision: 2
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"labels":{"app":"nginx"},"name":"nginx-deployment","namespace":"d...
Selector:               app=nginx
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:        nginx:1.17.3
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-579fcbbb4 (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  11m    deployment-controller  Scaled up replica set nginx-deployment-6b474476c4 to 3
  Normal  ScalingReplicaSet  4m45s  deployment-controller  Scaled up replica set nginx-deployment-579fcbbb4 to 1
  Normal  ScalingReplicaSet  4m32s  deployment-controller  Scaled down replica set nginx-deployment-6b474476c4 to 2
  Normal  ScalingReplicaSet  4m32s  deployment-controller  Scaled up replica set nginx-deployment-579fcbbb4 to 2
  Normal  ScalingReplicaSet  4m30s  deployment-controller  Scaled down replica set nginx-deployment-6b474476c4 to 1
  Normal  ScalingReplicaSet  4m30s  deployment-controller  Scaled up replica set nginx-deployment-579fcbbb4 to 3
  Normal  ScalingReplicaSet  4m20s  deployment-controller  Scaled down replica set nginx-deployment-6b474476c4 to 0
```

To describe what happened when we rolled out new version of nginx:

1. At first when we ran replicaset it created old replicaset (nginx-deployment-6b474476c4) to 3 when we first ran the deployment
2. Then we made change to the deployment file to add new version of nginx image.
3. It create new replica set (nginx-deployment-579fcbbb4) and added 1 pod on it.
4. It deleted 1 old pod with old nginx version from old replica set (nginx-deployment-6b474476c4).
5. It added 1 pod with new nginx on new replica set (nginx-deployment-579fcbbb4).
6. It again added 1 more pod with new nginx version on new replica set (nginx-deployment-579fcbbb4).
7. It scaled down the no of pods on old replica set nginx-deployment-6b474476c4) to be zero.

You can also change the update strategy by changing the parameter "RollingUpdateStragety". Currently it is set to
```
kubectl descibe describe deployments nginx-deployment

...
...
...

MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
...
...
...
```

### See the rollout history
```
kubectl rollout history deployment.v1.apps/nginx-deployment

deployment.apps/nginx-deployment 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

### See individual revision

#### See revision 1

```
kubectl rollout history deployment.v1.apps/nginx-deployment --revision 1


deployment.apps/nginx-deployment with revision #1
Pod Template:
  Labels:       app=nginx
        pod-template-hash=6b474476c4
  Containers:
   nginx:
    Image:      nginx:1.14.2
    Port:       80/TCP
    Host Port:  0/TCP
    Environment:        <none>
    Mounts:     <none>
```

#### See revision 2
```
kubectl rollout history deployment.v1.apps/nginx-deployment --revision 1


deployment.apps/nginx-deployment with revision #2
Pod Template:
  Labels:       app=nginx
        pod-template-hash=579fcbbb4
  Containers:
   nginx:
    Image:      nginx:1.17.3
    Port:       80/TCP
    Host Port:  0/TCP
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>

```

## Deployment configuration

Things that you need to know when performing rolling update.
1. MaxSurge <= Allows how many more container can we create a part from existing container to do a update
2. MaxUnavailable <= Specifies how much downtime is acceptable

For fully 0 downtime use
MaxSurge=25%
MaxUnavailable=0

For partial with no new pods creation
MaxSurge=0
MaxUnavailable=25%

You can edit these parameter using command
```
kubectl edit deployment DEPLOYMENT NAME
```