# Docker volumes

### Types
1. Bind mount
2. Volume

### Bind mount
It mounts a specific path on the host machine to the container. Not portable as it is dependent on host machine's file system. Storage location is manager by the user.

#### Bind mount using --mount flag
Create a text file index.txt and add some data in it.
```
docker container run --name busybox -d --rm --mount type=bind,source=/home/test/index.txt,destination=/root,readonly busybox cat index.txt
```

#### Bind mount using volume flag
In volume flag we just specify the name of the volume and let docker handle where it wants to keep it.

```
docker container run -v /home/user/text.txt:/root,ro busybox cat text.txt
```


### Volume
It stores data on the host file system, but the storage location is managed by docker. It is more portable. We can mount same volume to multiple containers.
It can be reused in multiple containers.

#### Volume mount Using --mount flags
```
docker container run --name busybox -d --rm --mount type=volume,source=my_volume,destination=/root,readonly busybox
```


#### Volume mount using volume flag
In volume flag we just specify the name of the volume and let docker handle where it wants to keep it.
```
docker container run -v my_volume:/root,ro busybox cat text.txt
```

### Using shared volume
Volume can be shared across multiple containers. 

SYNTAX:
docker container run --rm --mount type=volue,source=VOLUME-NAME,destination=/root,ro busybox

The VOLUME-NAME can be used across other containers as well.

```
docker container run --rm --name=container1 --mount type=volume,source=shared-volume,destination=/root,ro busybox


and again in another container

docker container run --rm --name=container2 --mount type=volume,source=shared-volume,destination=/root,ro busybox

```

### List volumes
You can list volumes using following command.
```
docker volume ls

DRIVER              VOLUME NAME
local               d5da914d4f177bc784968e4f5d919054b356134b52e109d1e7ca03890b133a42
local               df79188b11f6e9894ba54020b2926d4e0461cf1cc3968d21d13955c9a91d41d4
local               jenkins-data
local               jenkins-docker-certs

```

### Creating volume
```
docker volume create VOLUME_NAME
```

### Get information baout volume
```
docker volume inspect VOLUME_NAME
```

### Deleting volume
```
docker volume rm VOLUME_NAME
```

# Storage in cluster
When you are working with multiple docker machines, such as swarm cluster you may need to share data or volume between those machines.

You can use a volume driver to create that is external to any specific machine in your cluster.

Simple way to achieve that is using a simple docker plugin called sshfs.
You can install sshfs plugin using following command. 

```
docker plugin install --grant-all-permissions vieux/sshfs
```
This command needs to run on workers nodes as well.

Now create a volume with driver flag
```
docker volume create \
 --driver vieux/sshfs -o \
  sshcmd=user@PRIVATEIPOFSERVER:/home/user/external_storage \
  -o password=XXXXXX sshvolume
```

### list volume
```
docker volume ls
```

### To to make all services use external volumes
```
docker service create \
--replicas 3 \
--name storage_service \
--mount volume-driver=vieux/sshfs,source=cluster_volume,destination=/app,volume-opt=sshcmd=USERNAME@SERVERIP:/home/user/external,volume-opt=password=PASSWORD IMAGE
```