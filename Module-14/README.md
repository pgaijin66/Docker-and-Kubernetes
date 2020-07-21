### Enable autolock in swarm
```
docker swarm update --autolock=true
```

Take note of the unlock-key

### Run a command to interact with the swarm, then restart docker and try the command again to verify that the swarm is locked.
```
docker node ls
sudo systemctl restart docker
docker node ls
```

### Unlock swarm using unlock key and verify that it is unlocked
```
docker swarm unlock
docker node ls
```

### Obtain existing unlock-key
```
docker swarm unlock-key
```

### Rotate unlock key
```
docker swarm unlock-key --rotate
```

### Disable autolock
```
docker swarm update --autolock=false
sudo systemctl restart docker
docker node ls
```