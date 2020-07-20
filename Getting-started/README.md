# DCA

# Install docker on centos 7
```
sudo yum install -y device-mapper-persistent-data lvm3 # required package for Docker CE
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/deocker-ce.repo # addring docker repo
sudo yum install -y docker-ce-18.09.5 docker-ce-cli-18.09.6 containerd # install docker package
```

# Start and enable docker
```
sudo systemctl start docker # start docker
sudo systemctl enable docker # enable docker
sudo docker version #checking docker version
```

# Add user to run docker
```
sudo useradd docker-user
sudo usermod -a -G docker docker-user # Adding user to docker group to run docker
```

# Run simple container
```
docker run hello-world
```