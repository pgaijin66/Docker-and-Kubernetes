# Docker stack

### Docker service
Collection of containers

### Stack
 A stack is a collection of interrelated services that can be deployed and scaled as a single unit

Docker stacks are similar to multi-container application using docker compose.

### create a stack file
Create a docker-compose file and add following
```
version: '3'
services:
  web:
    image: nginx
    ports:
    - "8080:80"
  busybox:
    image: radial/busyboxplus:curl
    command: /bin/sh -c "while true; do echo $$MESSAGE; curl web:80; sleep 10; done"
    environment:
    - MESSAGE=Hello                                                                  
```

### Deploy a stack
You can deploy docker stack using the same compose file with following command.
docker stack deployt -c COMPOSE_FILE STACK_NAME
```
docker stack deploy -c docker-compose.yml simple

Creating network simple_default
Creating service simple_busybox
Creating service simple_web
```

### List current stacks
```
docker stack ls

NAME                SERVICES            ORCHESTRATOR
simple              2                   Swarm
```

### List task associated with a stack
```
docker stack ps STACK_NAME
```

### List services associated with a stack
```
docker stack services STACK_NAME

eg:
docker stack ps simple

ID                  NAME                   IMAGE                      NODE                DESIRED STATE       CURRENT STATE                     ERROR                              PORTS
l082pqg3fw45        simple_busybox.1       radial/busybox/plus:curl   node2               Ready               Rejected less than a second ago   "No such image: radial/busybox…"   
3iohevb1cqkd         \_ simple_busybox.1   radial/busybox/plus:curl   node1               Shutdown            Rejected 5 seconds ago            "No such image: radial/busybox…"   
jhm59s1yxgjr         \_ simple_busybox.1   radial/busybox/plus:curl   node2               Shutdown            Rejected 10 seconds ago           "No such image: radial/busybox…"   
k0lgiwlpt3g7         \_ simple_busybox.1   radial/busybox/plus:curl   node1               Shutdown            Rejected 15 seconds ago           "No such image: radial/busybox…"   
9r60dwdedy1i         \_ simple_busybox.1   radial/busybox/plus:curl   node2               Shutdown            Rejected 20 seconds ago           "No such image: radial/busybox…"   
o5441emaq9gw        simple_web.1           nginx:latest               node1               Running             Running about a minute ago                                           
```

### Service logs
```
docker service logs SERVICE_NAME
```

### Remove a stack
```
dockerstack rm STACK_NAME
```