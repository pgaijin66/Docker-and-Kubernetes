# Node Label

You can add node lable as a metadata to your swarm nodes.

You can determine which nodes the tasks or containers will run on.

Eg: With nodes in multiple datacenters or availability zones you can use lables to specify whcih zones each node is in. Then, execute tasks in specific zones or distribute them evenly across zones.



## Listing nodes
```
docker node ls

ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
dgvbga80w0bq0zghqvomcntxb *   node1               Ready               Active              Leader              19.03.12
pu0cmt0myl7hz2lhoma2pgql1     node2               Ready               Active                                  19.03.12
```


## Adding lable to node
```
docker node update --label-add LABEL=VALUE NODE
```

docker node update --label-add availability_zone=east <NODE_NAME>
docker node update --label-add availability_zone=west <NODE_NAME>

```
 docker node update --label-add availability_zone=west node2
 docker node update --label-add availability_zone=west node2
```

## View existing node lables
```
docker node inspect --pretty NODE
```

## Node contstraints
To run a service tasks only on nodes with a specific label value, we use node constraints. We can use node constraints to specify AZ.

Use --constraint flag with docker service create

docker service create --constraint node.labels.LABEL==value IMAGE

eg:
```
docker service create --constraint node.labels.availability_zone=east nginx
```

To run container to run on nodes without particular node label value
```
docker service create --constraint node.label.availability_zone!=east nginx
```

You can use --constraint flag multiple times to list multiple constraints. All constraints must be satisfied for tasks to run a node.

## Placement-perf
It is used to evenly balance tasks or containers across all the values of the node label. 

Use --placement-perf with spread strategy to spread tasks evenly across all values of a particular label

```
docker service create --name nginx-spread --placement-perf spread=node.label.availability_zone --replicas 3 nignx
```
This command try to evenly balance tasks across all nodes which satisfies the label.

if you have a lavel availability_zone with three values (east,west and south) the tasks will be divided evenly among the node groups with each of those three values, no matter how many nodes are in each group.