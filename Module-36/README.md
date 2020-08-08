# K8s Networking model

k8s imposes following:
1. Pods on a node can communicate with all pods on all nodes without NAT
2. All Nodes can communicate with all pods without NAT
3. It the pod sees itself as is the same ip that others see it as.


# K8s networking challenges and solutions
### container to container
### pod to pod networking
### pod to service
### internet to service


### container to container

happens inside port
Group of containers with same IP addres
Communication betwn containers via the localhost followed by port.


### pod to pod networking

Pod has interface i.e eth0 which connects to virtual ethernet veth0 which connects to bridge (docker0)


### pod to service

Use service for abstraction which can provide single ip addres and DNS through which pods can be accessed.

Endpoints tracks the ip address of the objects

Multiple pods which different ip, create gateway, gateway keeps track of each pods through endpoint


### internet to service

Uses kubernetes ingress.

Ingress is a collection of routing rules which governs how external users access the service running within the k8s cluster.

Does route based, path based routing as well.

Can also a ingress controller.