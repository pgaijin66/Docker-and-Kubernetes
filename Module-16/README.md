# Services

A service is used to run application in docker swarm. 
A service might consist of single or multiple docker containers or taks. 
These tasks will be distributed automatically across the nodes in the cluster and executed as containers.

### Create service in swarm
```
docker service create [OPTION] IMAGE ARGS
```

Arguments
--replicas : specify the number of replica tasks to create for the service
--name: Specify a name for that service
-p PUBLISHED_PORT:SERVICE_PORT: It publishes port so that service can be accessible from outside or externally. This port is published on everynoe in swarm.

```
docker service create --name nginx --replicas 3 -p 8080:80 nginx
```

### List services running
```
docker service ls             
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
jzbvyq79qn4t        nginx               replicated          3/3                 nginx:latest        *:5000->80/tcp
```


### Templates
Templates can be used to give somewhat dynamic values to some flags with docker service create

Some accepted flags for templates are:
1. --hostname
2. --mount
3. --env

In following command we set an environment variable for each container that contains the hostname of the node that container is running on
```
docker service create --name node-hostname --env NODE_HOSTNAME="{{.Node.Hostname}}" --replicas 3 nginx

koiisdvdphbqzeetecoaei9i8
overall progress: 3 out of 3 tasks 
1/3: running   [==================================================>] 
2/3: running   [==================================================>] 
3/3: running   [==================================================>] 
verify: Service converged 
```

### Execute command on running container
```
docker exec CONTAINERID COMMAND

eg:
docker exec 4898c2c0f28f printenv
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=4898c2c0f28f
NODE_HOSTNAME=docker-desktop
NGINX_VERSION=1.19.1
NJS_VERSION=0.4.2
PKG_RELEASE=1~buster
HOME=/root
```


### Manage docker swarm service

#### List service
```
docker service ls
```

#### List service's tasks (containers)
```
docker service ps SERVICE
```

#### Get more information about a service and make it more readable
```
docker service inspect SERVICE --pretty
```

#### Make changes to service
```
docker service update [OPTIONS] SERVICE
e.

docker service update --replicas 3 nginx
```

#### Delete service
```
docker service rm SERVICE
```

## Replicated vs Global services

#### Replicated services
It runs the requested number of replicas task across the swarm cluster
```
docker service create --replicas 3 --name nginx nginx
```

#### Global services
It creats tasks or containers on each of the worker nodes on the cluster. If we add new node to cluster it will automatically run tasks on the cluster.
```
docker service create --mode global nginx
```

## Scaling services
Scaling services means chaning the number of replica tasks
```
docker service update --replicas REPLICAS SERVICENAME
```

We can scale using another way using docker service scale command
```
docker service scale nginx=4
```