# Learn
1. What is storage drivers?
2. Select storage driver (overlay or devicemapper)

# Selecting Storage driver

By default docker uses overlay storage drive

Check using 
```
docker info | grep Storage
```

To change the storage driver, add which storage driver to use in docker.service file

```
vi /usr/lib/systemd/system/docker.service # for Centos
```

Find EcecStart and add following
```
--storage-driver=devicemapper
```

or you can change the Storage driver value in docker.daemon in /etc/docker/docker.daemon