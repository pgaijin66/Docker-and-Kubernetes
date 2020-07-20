# Docker images

## What is docker image ?
A docker image is a file containing the code and components needed to run software/application in a container.

Both container and images uses layered file system. Each layer containers only the difference from previous layer.

The image consists of one or more read-only layers, while the container adds on addition wirtable layer.

![alt text](https://docs.docker.com/storage/storagedriver/images/container-layers.jpg)


When we run 
```
docker run nginx:1.15.8
```
The nginx:1.15.8 is the image name and we want to run container from this image.

## Layered file system

Image and container are made of up of multiple layers of file system. Layered file system allows multiple images and containers to share the same layers. This results in:.
1. Smaller overall storage footprint.
2. Faster image transfer.
3. Faster image build.

For example we can run two nginx container using same image and they use the same file system as the initial system.

## See layers of image
You can view the layers of image using command
```
docker image history nginx

IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
0901fa9da894        9 days ago          /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B                  
<missing>           9 days ago          /bin/sh -c #(nop)  STOPSIGNAL SIGTERM           0B                  
<missing>           9 days ago          /bin/sh -c #(nop)  EXPOSE 80                    0B                  
<missing>           9 days ago          /bin/sh -c #(nop)  ENTRYPOINT ["/docker-entr…   0B                  
<missing>           9 days ago          /bin/sh -c #(nop) COPY file:0fd5fca330dcd6a7…   1.04kB              
<missing>           9 days ago          /bin/sh -c #(nop) COPY file:1d0a4127e78a26c1…   1.96kB              
<missing>           9 days ago          /bin/sh -c #(nop) COPY file:e7e183879c35719c…   1.2kB               
<missing>           9 days ago          /bin/sh -c set -x     && addgroup --system -…   63.3MB              
<missing>           9 days ago          /bin/sh -c #(nop)  ENV PKG_RELEASE=1~buster     0B                  
<missing>           9 days ago          /bin/sh -c #(nop)  ENV NJS_VERSION=0.4.2        0B                  
<missing>           9 days ago          /bin/sh -c #(nop)  ENV NGINX_VERSION=1.19.1     0B                  
<missing>           5 weeks ago         /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B                  
<missing>           5 weeks ago         /bin/sh -c #(nop)  CMD ["bash"]                 0B                  
<missing>           5 weeks ago         /bin/sh -c #(nop) ADD file:4d35f6c8bbbe6801c…   69.2MB              
```

## List docker images
```
docker image ls -a
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
nginx                          latest              0901fa9da894        9 days ago          132MB
busybox                        latest              c7c37e472d31        2 weeks ago         1.22MB
prom/prometheus                latest              9f345bfa8fef        3 weeks ago         142MB
prom/prometheus                <none>              396dc3b4e717        4 weeks ago         142MB
prom/prometheus                <none>              39d1866a438a        5 weeks ago         142MB
prom/prometheus                <none>              49130a5a0755        5 weeks ago         140MB
jenkinsci/blueocean            latest              97e1aeaf7485        8 weeks ago         567MB
docker                         dind                beb378acf629        2 months ago        225MB
prom/prometheus                <none>              de242295e225        2 months ago        140MB
```

## Pulling images from docker hub
You can just download image to local system using following command
```
docker image pull [image][:tag]
```
eg. docker image pull nginx:10.10.1