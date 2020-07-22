# Storage driver

Types of Storage models
1. Filesystem storage
	used by overlay and aufs
	effecient use of memory

2. Block storage
	Used by device mapper
	Effecient for write-heavy workload

3. Object Storage
	Used in conjunction with filesystem storage and block storage.
	Flexible and scalable

# check storage driver
```
docker container inspect CONTAINERIF
```

# Data storage in image and container
```
/var/lib/docker/overlay2/....
```


# Device mapper
Device mapper is  one of the storage drivers available  for some linux distributions. It is the default storage driver for centos 7 and other distros.

Device mapper supports 2 modes.
1. loop-lvm mode
2. direct-lvm mode

# check storage driver info for docker daemon
```
docker info
```

# Configuring device mapper
1. stop docker
2. remove other existing docker data sudo rm -rf /var/lib/docker
3. open /etc/docker/daemon.json
4. Change the storage driver to device mapper and add following. This uses direct-lvm mode
```
{
  "storage-driver": "devicemapper",
  "storage-opts": [
    "dm.directlvm_device=/dev/nvme1n1",
    "dm.thinp_percent=95",
    "dm.thinp_metapercent=1",
    "dm.thinp_autoextend_threshold=80",
    "dm.thinp_autoextend_percent=20",
    "dm.directlvm_device_force=true"
  ]
}
```
5. enable and strat docker again.