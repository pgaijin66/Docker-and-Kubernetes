# DaemonSet

It ensues that all Nodes run a copy of a pod

## Use case 

If there are 3 worker node, and we can to run single copy of pod in every node. This is similar to creating service in Global mode.

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      # this toleration is to have the daemonset runnable on master nodes
      # remove it if your masters can't run pods
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd-elasticsearch
        image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

## Get daemonset

```
kubectl get daemonset
```

## Describe daemonset
```
kubectl describe daemonset
```

## Taints and tolerations

## Taints

Taints are used to repel the pods from a specific node. If taint has been applied to node, and pod is scheduled to worker node, it will not the pod on tainted worker node.


Check the taint flag on the output

```
kubectl describe WORKERNODE
```

Apply taint so that pods are not scheduled in the tainted worker node.
```
kubectl taint nodes NODENAME key=value:NoSchedule
```

## Toleration

In order to pass pods on the tainted worker node, we need to have a pass. This is called toleration

This pass is called Toleration

You pass toleration to the yaml file when we create a deployment file with key, operator and effect,
```
tolerations:
- key: "key"
  operator: "Exists"
  effect: "NoSchedule"
```