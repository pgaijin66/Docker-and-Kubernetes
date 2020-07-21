# Flatting images into single layers
Flattening the docker image is not manually supported by docker as it voilates the feature of layerd filesystem.

You can do that by exporting the container and then importing the container into an image

### Run the container
```
docker container run alpine:3.9.3 echo hello "Hello, World!"
```

### Check layers of image
```
docker image history
```

### Export docker container
```
docker export CONTAINER_NAME > CONTAINER.tar
```

### Import tar export into image
```
docker import - NEW_IMAGE:latest < cat CONTAINER.tar

or 

cat CONTAINER.tar > docker import - NEW_IMAGE.tar
```