# Docker swarm

It is a feature of docker which allows you to build distributed cluster of docker machines to run your containers. Provides orchestration, high-availability and scaling

# Docker swarm

Docker swarm consists of manager node and worker node. We can have as many worker node as we want.

## Swarm manager node
Manager node is responsible for controlling and orchestrating the docker worker nodes.

## Swarm worker node
Worker nodes are responsible for running containers. It handles the workload.

# Swarm Setup
First of all, Intall docker same like before on both nodes (Look at getting started module).

### Initialize manager node 
Execute following command on one node. This initializes swarm and makes it manager node.
```
docker swarm init --advertise-addr 192.168.10.10
```

This will give you output like this
```
Swarm initialized: current node (5mzrfeap9liiz4nbuabdq986t) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-69hk2x2m113oqzc7y573k72l1grazagte4g6vevs6fczg7iggt-dc4yp4rwur5au20ph64mk6y6i 192.168.10.10:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

```

It provides you with token, keem in find. If you lost the token you can issue  following command to join worker node to master. Copy and paste this token to worker node.
```
docker swarm join-token worker

 docker swarm join --token SWMTKN-1-69hk2x2m113oqzc7y573k72l1grazagte4g6vevs6fczg7iggt-dc4yp4rwur5au20ph64mk6y6i 192.168.10.10:2377
```

### List nodes in swarm
```
docker node ls
```

### Initialize worker node 
In order to setup worker node, to create a worker node we issue following command. 
```
docker swarm join --token SWMTKN-1-69hk2x2m113oqzc7y573k72l1grazagte4g6vevs6fczg7iggt-dc4yp4rwur5au20ph64mk6y6i 192.168.10.10:2377
```
Once you issue this command it will say node is joined to swarm as worker. You can see all nodes using command show below. Note you can run this command only on manager node.
```
root@node1:~# docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
fmy5bh7rmz70dsrtlbz6fzxiv *   node1               Ready               Active              Leader              19.03.12
2qp5awxbp80r2mtiole3p907f     node2               Ready               Active                                  19.03.12

```

