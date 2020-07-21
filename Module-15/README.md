# High availability in swarm cluster
In a production scenario, it is a good idea to consider fault tolerance when designing a swarm cluster. In this lesson, we will discuss some of the considerations for high availability when it comes to Docker Swarm clusters, such as how multiple swarm managers maintain consistent swarm state and how to distribute manager nodes across multiple availability zones.

## Alrogithm to maintaing consistent cluster state
Raft consensus algorithm

## Quorum
Quorom is the majority (more than half) of the manager in swarm. For eg. For a swarm with 5 manager, the quorum is 3

A quorun nust be maintained in order to make chanegs to the cluster state. If a quorum is not available, nodes cannot be added or removed, new tasks cannot be added, and existing tasks cannot be changed or moved.

Note that since a quorum requires more than halk of the manager nodes, it is recommended to have an odd number of managers.

# Always have odd number of namager nodes.

### Availability zones

Docker recommends that you distribute your manager nodes accross at least 3 availability zones.