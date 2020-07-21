# Manage images

### Pull image
```
docker image pull nxingx:1.14.0
```

### List image
```
docker image ls
```

### List image
```
docker image ls # -a images shows the intermediate images.
```

### List image
```
docker image ls # -a images shows the intermediate images.
```

### Inspect image
Gives you json data of that image
```
docker image inspect nginx:1.14.0
```

### Inspect image in custom templage
Gives you json data of that image
```
docker image inspect nginx:1.14.0 --format "{{.Architecture}}" # Format uses go templating
```

### Delete image
Make sure image is not used by containers
```
docker image rmi nginx:1.15.11 # -f to force deletion
```

### Dangling images
Images with no tags and no repositories
To remove them
```
docker image prune
```