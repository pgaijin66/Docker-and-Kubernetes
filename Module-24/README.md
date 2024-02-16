# Docker networking

## Container networking model
It is used by docker to manage networking in docker containers.

### Ingress network
Is is a service used by docker services when no network is specified or ingress network in docker network ls command is the default network for docker services.

## Docker network commands
##### create network
```
docker network create my-net
```

##### add network to container
```
docker container run -d --rm --name busybox --network my-net radian/busyboxplus:curl sleep 3600
```

##### Create another container
```
docker container run -d --rm --name  my-nginx
```

##### Connect my-nginx container to network
```
docker network ls
docker network connect my-net nginx
```

##### Inspect network
```
docker network inspect my-net
```

##### Disconnect a running container from an existing network
```
docker network disconnect my-net my-net-nginx
```

##### Delete network
Before deleting first disconnect all container connected to that network the  only delete the network. Do not use force command.
```
docker network rm my-net
```

## CNM uses following:
1. Sandbox : Isolated unit containing all networking components associate with a single container using namespaces
2. Endpoint : Connect sadbox to a network Each sandbox can have multiple endpoints
3. Network : Collection of endpoints connected together to one another

## Docker uses
1. Network driver : handles actual implementation of CNM concepts.
2. IPAM drivers : Responsible for allocating IP and subnet

## Types of network
1. Bridge network
2. Overlay network
3. MACVlan network
4. Host network
5. None

### Usage
You can use --net=DRIVER

### Host network
It allows container to use the host network stack directly. Containers directly use host network interface. All the containers on the host using the host network driver share the same network namepace. Can be used for one or only few containers on a single host. Container network interface are directly connected to the host network interface
```
docker container run -d --rm --name nginx --net host nginx
```

### Bridge network
It uses linux brdige interfaces to provide connectivity to containers and host. This is the default driver for container running on a single host. It createas default linux bridge network called bridge0. Containers automatically connect to tihs if no other network is specified. Use case is isolated networking among containers on single host.


create network
```
docker network create --driver bridge my-bridge
```

List network
```
docker network ls
```

```
docker container run -d --rm --name my-bridge --net host nginx
```

### Overlay network
Overlay network driver provides connectivity between containers across multiple docker hosts.
Automatically configured network interface, bridges etc on each host as needed
Use case is networking between containers

Default network setting
create network
```
docker network create --driver overlay --attachable my-overlay-network 
```

List network
```
docker network ls
```

```
docker container run -d --rm --name my-overlay --net my-overlay-overlay nginx
```

Create service with in docker
```
docker service create --network my-overlay-network --name overlay-service --replicas 3 nginx
```


### MACLVAN network
It help lightweight implementation by connecting container interface directly to host interface. container interface connect to MAC VLAN and then MACVLAN interafce connects to host network interface Harder to configure and greater dependency between MACVLAN and external network.

Use case: When there is a need for extremely low latency or a need for containers with IP addresses in the external submet.


create network
```
docker network create --driver maclvan --subnet 192.168.0.0/24 --gateway 192.168.0.1 -o parent=eth0 my-macvlan-network
```

List network
```
docker network ls
```

```
docker container run -d --rm --name my-bridge --net host nginx
```


### None network
Does not provide any networking to containers. Containers is completely isoplated from other containers and the host. If you want networking with the None driver, you must set everything up manually. None does not create a speperate networking namespace for each container, but no interface or endopints.
Use case: When there is no need for container networling pr you want tp set all of the netorking up yourself.


```
docker container run -d --rm --name my-bridge --net none nginx
```

## Embedded DNS
Is is a docker network implementation of an embedded DNS server, allowing containers and services to locate and communicate with one another using conintaer names.

Containers can communicate with other container and services using services or container name or network alias

```
docker container run --name nginx --network my-net -p 80:80 --network-alias my-nginx-alias nginx

```
Now create another continaer and try to connect to it using container network alias.

```
docker exec my-net-busybox curl my-nginx-alias:80
```

Creating aliasses and connecting using network aliases
```
docker container run -d --name m-net-nginx3 nginx
docker network connect --alias another-alias my-net my-net-nginx3
```

# Exposing port

Expose container by publishing ports. 

```
docker run -d -p 8080:80 --name nginx_pub nginx
```

### List port mapping of container
```
docker port CONAINTER_ID
```

#### Display port
```
docker ps
```

## Publishing ports for services

Mode:
1. Ingress
2. Host

### Ingress
THis is default if no mode is specified.
Uses routing mesh, which means published port listens on every node in the cluster and transparently directs incoming traffic to any task that is part of that service on any node.
It publishes port on all the nodes connected to that network. I.e if we pulblish port 8080 on a service then if service is running on other nodes it will automatically publish port 8080 on other nodes as well.

To publish a service using host mode
```
docker service create --publish mode=ingress.target=80,published=8080 --name nginx_host_pub nginx
```

### Host mode
THis mode publishes the port directly on the host where a task is running. Cannot have a multiple replicas on the same node if we use static port
Traffic to the published port on node foes directly to task running on that specific node.

To publish a service using host mode
```
docker service create --publish mode=host.target=80,published=8080 --name nginx_host_pub nginx
```


# Network Troubleshooting

### container Logs
```
docker logs CONTAINER
```

### service Logs
```
docker service logs SERVICENAME
```

### Docker daemon Logs
```
journalctl -u docker
```

### Using Netshoot
Used to troubleshooting networking issue
Use image: nicolaka/netshoot

Sping up a netshoot container and attach to network which is faulty
```
docker run --rm --network custom-net nicolaka/netshoot curl custom-net-nginx:80
```

## Configure docker to use external DNS
You may need to customize the external DNS server used by your containers.

### Customize default DNS in docker host
You can change the default fot host with DNS setting in daemon.json
```
{
	"dns":["8.8.8.8"]
}
```

restart docker daemon
```
systemctl restart docker
```

Do a dnslookup
```
docker run nicolaka.netshoot nslookup google.com
```

### Configure container to use external DNS server
```
docker run --dns DNS_ADDRESS

docker run --dns 8.8.4.4 nicolaka/netshoot nslookup google.com
```