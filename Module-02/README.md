# Running a container

## Running a simple hello-world container
```
docker run hello-world
```

## Adding tags
Without tags, docker selects the latest version of image
```
docker run nginx:1.15.11 # value after : is a tag
```

# Disecting output
From the above command we got the following output. Let's see what it means
```
Unable to find image 'nginx:1.15.11' locally
1.15.11: Pulling from library/nginx
27833a3ba0a5: Pull complete 
eb51733b5bc0: Pull complete 
994d4a01fbe9: Pull complete 
Digest: sha256:50174b19828157e94f8273e3991026dc7854ec7dd2bbb33e7d3bd91f0a4b333d
Status: Downloaded newer image for nginx:1.15.11
```

1st line: Docker generally checks if the image is available in your local system. If its there then it runs it from there. If its not available then it checks docker registry i.e dockerhub and pull the image. 

2ns line: Here docker is pulling nginx from docker hub. You can specify which registry to use if you don't want to use docker hub.

3,4,5th line: Docker uses something called UFS (Union File System). It is also called layered file system. All chanages to docker image or container are adding on layers. If we create a container and make some changes in it then it will add another layers on top of it with that change.

6th line: Once all layers of image are pulled, they are compiled into single and it creates hash for image which is unique.

7th line: It tell us downloading docker container is successfull and it now runs container from our pulled image. When we ran hello-world container,it did the same thing. Here nginx is supposed to run and strat web server. Hence our container for nginx has been started and waiting for connection

# Cancel running container
Press CTRL + C to stop the container

# Specify commands to be executed when docker container runs
Let's run a container called busybox
```
docker run busybox echo hello-world
```

Here we added some extra arguments called echo hello-world. In order to provide what command to run.

# Running docker container in background
TO run container in background we issue a "-d" flag. It is called running container in detached mode.
```
docker run -d nginx
```
Now it runs container in background. This is usefull when you want some services to run in background. It gives you a hash id of your container like this
```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
8559a31e96f4: Pull complete 
1cf27aa8120b: Pull complete 
67d252a8c1e1: Pull complete 
9c2b660fcff6: Pull complete 
4584011f2cd1: Pull complete 
Digest: sha256:a93c8a0b0974c967aebe868a186e5c205f4d3bcb5423a56559f2f9599074bbcd
Status: Downloaded newer image for nginx:latest
bb62d0d41e216f8b383aaed801565a96024091c232a648de88701ca7d4e7b468  # < === This is the container id
```

# Check running containers
```
docker ps
```
It gives you output like this
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
bb62d0d41e21        nginx               "/docker-entrypoint.…"   43 seconds ago      Up 42 seconds       80/tcp              suspicious_varahamihira
```

# Stopping docker container
To stop container you issue docker container CONTAINER_ID
```
docker container bb62d0d41e21
```

# Listing docker containers
List docker container using following:
```
docker ps
```

# removing container
```
docker container rm [CONTAINERID]
```

# Docker Flags
### --name
Docker randomly assigns name into the container. In order to override that we use flag --name

### --restart
if the container stops, the it will not start again. If you want your container to restart when it stops then we issue flag --restart <br>
Options available with --restart switch
	1. on-faulire = it restarts container if it stopped with non-zero exit code.
	2. always = It restarts container in any case. Does not checks the status codes of the failure. Starts container automatically on daemon startup
	3. unless-stopped =  Similar to always, it restarts the container, it keeps on running container even if daemon restarts unless we manually stop.

### -p 
It means publish. We need to pulish ports from container to our local system. We are exposing port to host. systax is --p [hostport]:[containerport] eg. -p 8080:80

### --rm
It means delete the container when it stops. By default docker keeps log of all the container that ran, which might take up a lot of space and might make docker ps output confusing to understad. If you want to delte the container when it stop, we add the --rm flag to the docker container command.

### --memory
Hard limit on memory usage for container. if we want to run container and allow it to only use max of 500 MB memory then we use this flag. eg --memory 500M

### --memory-reservation
Soft limit on memory usage. The container will be restricted to this limit if docker detects memory contention (not enough memoy on the host). eg: --memory-reservation 256M

# Running nginx container
Now run the container with following commands.
```
docker container run -d --rm --name nginx -p 8080:80 --memory 500M nginx
```
You will get the container id value, and since we exposed docker container ports to host port. We can go to browser and see it our nginx container is running at port 808 as we exports container 80 port to 8080 on host.