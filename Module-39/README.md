# Labels and Selectors

## Labels are key/value pairs that are attached to objects, such as pods.
name: database
env: prod

## Selectors allows us to filters objects based on labels

### Labels and selectors in k8s

Add labels to pods to define which environment they are from.

#### List pods on  only dev environment
```
kubectl get pods -l env=dev
```

### Adding label to pod

```
kubectl label pods POD1_NAME env=dev
kubectl label pods POD2_NAME env=prod
```

env=dev is the label

### Show labels
```
kubectl get pods --show-labels
```

### We add labels to k8s object in metadata section.