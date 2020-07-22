# Docker image cleanup

### How to clean up image that is no longer used or needed.

### See amount of storage used by docker used by images
```
docker system df -v
TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
Images              43                  10                  6.191GB             5.841GB (94%)
Containers          16                  0                   8.212MB             8.212MB (100%)
Local Volumes       4                   1                   312.4MB             312.4MB (100%)
Build Cache         0                   0                   0B                  0B
```

### Removing dangling images
It removes the images that are not referenced by any tags or containers
```
docker image prune
```

### Remove all unused images
```
docker image prune -a
```