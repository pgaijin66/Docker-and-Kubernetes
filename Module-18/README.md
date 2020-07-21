# Docker compose

It is a tool that allows you to run multi-container applications on a single node defined using declerative format. It is similar to stack which allows you to run containers in multiple nodes.

It uses YAML files to declarativey define a set of containers and other resource that will be creaated as part of the larger application.

### First install docker compose
Use following command to run docker compose
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose version
```

### Setting up a docker-compose directory
1. First create a directory for docker compose
2. Create a docker-compose.yml file
```
mkdir nginx-compose && cd nginx-compose
touch docker-compose.yml
sudo chmod +x /usr/local/bin/docker-compose 
docker-compose version
```

### Creating simple docker-compose application
Add following code to your docker-compose.yml file
```
---
version: '3'
services:
  web:
    image: nginx
    ports:
      - "8080:80"
  redis:
    image: redis:alpine
```

Run services using 
NOTE: In order to run docker-compose command you need to be in the directory which consist of docker-compose file.
```
docker-compose up -d


WARNING: The Docker Engine you're using is running in swarm mode.

Compose does not use swarm mode to deploy services to multiple nodes in a swarm. All containers will be scheduled on the current node.

To deploy your application across the swarm, use `docker stack deploy`.

Creating network "nginx-compose_default" with the default driver
Pulling redis (redis:alpine)...
alpine: Pulling from library/redis
df20fa9351a1: Pull complete
9b8c029ceab5: Pull complete
e983a1eb737a: Pull complete
18cbc0ec1809: Pull complete
a6ec7a0dc6d8: Pull complete
e7cefbc91f72: Pull complete
Digest: sha256:b7561e4e994ab52cb2062b9c3e943ab2af3287caf9d9aeb6719acc77013769a5
Status: Downloaded newer image for redis:alpine
Creating nginx-compose_redis_1 ... done
Creating nginx-compose_web_1   ... done
```

### Check services running in docker-compose
```
docker-compose ps

        Name                       Command               State          Ports        
-------------------------------------------------------------------------------------
nginx-compose_redis_1   docker-entrypoint.sh redis ...   Up      6379/tcp            
nginx-compose_web_1     /docker-entrypoint.sh ngin ...   Up      0.0.0.0:8080->80/tcp
```

### Shutdown all services
```
docker-compose down

Stopping nginx-compose_web_1   ... done
Stopping nginx-compose_redis_1 ... done
Removing nginx-compose_web_1   ... done
Removing nginx-compose_redis_1 ... done
Removing network nginx-compose_default
```