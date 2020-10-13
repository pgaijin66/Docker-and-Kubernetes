# Dockerfile

## What is Dockerfile ?

A Dockerfile is a set of instructions which are used to construct a Docker image. These instructions are called derictives. If you want to create your own image then you need to write from code and build image from it. All code to create a image is written in Dockerfile. If you want to run custom software using docker then you need to create a Dockerfile and build image from that Dockerfile. 

Once image is built you can use that image to run containers.

There are 4 main directives that you must have in your Dockerfile. They are
1. FROM
2. ENV
3. RUN
4. CMD

#### FROM

You start to build a stage and set the base image you want to work with. Usually must be the first directive in the Dockerfile (except the ARG which can be placed before FROM)

#### ENV

You can configure environment variables using ENV directive. These can be referenced in the Dockerfile itself and are visible to the container at runtime.

#### RUN

This creates new layer on top of the base image or previous layer by running a command inside that new layer and committing the changes.

#### CMD

Specify a default command used to run a container at executino time.

### Let's create a custom nginx image from scratch.
Create a index.html file which will be the base landing page for our nginx container.

Go ahead and create a Dockerfile and add following code in the file.
```
# Simple nginx image
FROM ubuntu:bionic

ENV NGINX_VERSION 1.14.0-0ubuntu1.3

RUN apt-get update && apt-get install -y curl

RUN apt-get update && apt-get install -y nginx=${NGINX_VERSION}

CMD ["nginx", "-g", "daemon off;"]
```

Now to build image from Dockerfile we use docker build command.
```
docker build -t custom-ningx .

 kryptoomega@192-168-1-106  ~/Documents/Learn/DCA/Module-9  docker build -t custom-image:v0.1 .
Sending build context to Docker daemon  5.632kB
Step 1/5 : FROM ubuntu:bionic
bionic: Pulling from library/ubuntu
a1125296b23d: Pull complete 
3c742a4a0f38: Pull complete 
4c5ea3b32996: Pull complete 
1b4be91ead68: Pull complete 
Digest: sha256:e5b0b89c846690afe2ce325ac6c6bc3d686219cfa82166fc75c812c1011f0803
Status: Downloaded newer image for ubuntu:bionic
 ---> d27b9ffc5667
Step 2/5 : ENV NGINX_VERSION 1.15.8
 ---> Running in d46ab37e3767
Removing intermediate container d46ab37e3767
 ---> 18b715cc2491
Step 3/5 : RUN apt-get update && apt-get install -y curl
 ---> Running in 66507726dbef
Get:1 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]
```

Each directive specifeid is shown as a step during the build process. Once it's completed. You can rerun the cmmand and it will reuse the layered file system that was created.

## Run your image

You can view the image created using docker image ls commant
```
docker image ls
                
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
custom-image                   v0.1                69e8d01e6223        7 seconds ago       165MB
<none>                         <none>              cb159dd8c8f6        2 minutes ago       108MB
```

Now you can run container from you newly create image.
```
docker container run -d --rm --name nginx -p 8080:80 --memory 500M custom-image:v0.1
```

Do a docker ps to see your container
```
docker ps                                                                                  
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
d1032c4f26fd        custom-image:v0.1   "nginx -g 'daemon of…"   8 seconds ago       Up 7 seconds        0.0.0.0:8080->80/tcp   custom-nginx
```

Also, check in your web browser

### Additional docker directives

create folder and create inde.html inside it and create a Docerfile ad add follwing into that Dockerfile
```
# custom nginx image with custom index.html
FROM ubuntu:bionic

ENV NGINX_VERSION 1.14.0-0ubuntu1.3

RUN apt-get update && apt-get install -y curl
RUN apt-get update && apt-get install -y nginx

WORKDIR /var/www/html

# add cab download fiel and archive it
ADD index.html ./

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

# it uses this signal to stop the container
STOPSIGNAL SIGTERM
HEALTHCHECK CMD curl localhost:80
```

#### WORKDIR
It specified which is the directory which you run the container

#### EXPOSE
It exposes the port

#### STOPSIGNAL
Specified the signal that will be used to stop the container

#### HEALTHCHECK
It is useful to run to perform custom health check to verify it the container is working properly or not.

Now we can build the container using the same command as we did before
```
docker build -t my-nginx-image .
```

Once its built we can run the container from this image.
```
docker run -d -p 8080:80 my-nginx-image
```

Now go to the browser to see if our container is running or not. If you see the index.html file in the url localhost:8080 then our container is running properly.

Once you are done stop container and delete it to clean up.