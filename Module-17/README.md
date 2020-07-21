# Docker inspect

### Inspect docker container
```
docker CONTAINERID inspect --pretty
```

### Inspect docker service
```
docker SERVICENAME inspect --pretty
```

### Inspect using --format to get custom output
```
docker service inspect --format="{{.ID}}" SERVICENAME
```