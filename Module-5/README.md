# Backing up  and Restoring docker swarm

## Backup
1. Stop docker setvice
```
systemctl stop docker
```
2. Create a tar of swarm directory
```
tar -zvcf backup.tar.gz /var/lib/docker/swarm
```
3. start docker service again
```
systemctl start docker
```

## Restore
1. Get the backup on the server.
2. stop docker service
3. Remove the contents of /var/lib/docker/swarm
```
rm -rf /var/lib/docker/swarm/*
```
4. Extract backup into that directory
```
tar -zxvf backup.tar.gz -C /var/lib/docker/swarm/
```