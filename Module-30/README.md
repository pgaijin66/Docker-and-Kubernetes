## Replica set

The pod that we created before does not has its deployment created. As discussed before, the deployments basically passes record of intent for a given k8s object to make sure that it is always running at given time. This is similar to replicas directive that you provide in docker configuration file.

ReplicaSet purpose is to always maintain a given set of replicas of Pods running at any given time.

Suppose you want your nginx container created above to always run 3 instance or sets of pods then you provide ReplicaSet.

There are two things that you need to remember while creating ReplicaSet. They are
1. Desired State => It specifies what is the state of pods which is desired at any given time,
2. Current State => The actual state of pods which are running

If we have
Desired State = 3
Current State = 2
Image = nginx

Replica set always tries to maintain 
```
Desired State = Current State
```

So ReplicaSet will spawn another container to maintain the above mentioned condition.

You can get the replicaset using following command
```
kubectl get replicaset
```

## Creating replica set
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  # Selector should match the labels associated with pods
  selector:
    matchLabels:
    # This is for replica set to count how many replicas of replicaset is running
      tier: frontend
  # Using template you do not need to specify apiVersion and Kind
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
```

Now run the replicaset using command
```
kubectl apply -f apps.yaml
```

To see the replica sets
```
kubectl get rs

or 

kubectl get replicaset

NAME       DESIRED          CURRENT         READY   AGE
frontend   3                3               3       14m
```

If you want to see the pods created from the replica set with the labels defined use following command
```
kubectl get pods --show-labels

NAME             READY   STATUS    RESTARTS   AGE   LABELS
frontend-2jp4k   1/1     Running   0          15m   tier=frontend
frontend-jnmsl   1/1     Running   0          15m   tier=frontend
frontend-lbnnq   1/1     Running   0          15m   tier=frontend
```

Now, go ahead and delete a pod to see if replica set maintains the specified number of replicas

Delete a pod
```
kubectl delete pod POD_NAME
```

Now, if you do get pods, you can see new pod has already been spawned in AGE field.


### Deleting a replica set
```
kubectl delete rs REPLICASET_NAME
```

## Selector label should match lables on templates metadata labels that you assign.

# Challenges in ReplicaSets

Replicaset works well for basic functionality for managing pods, scaling pods etc, For more adavanced functionality like rolling changes it is recommended to make sure of deployments.