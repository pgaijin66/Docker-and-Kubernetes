***INSTALL DOCKER***

sudo apt-get update

sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io

***INITIALIZE SWARM***

sudo docker swarm init --advertise-addr 10.128.0.2

sudo docker swarm join-token worker

sudo docker swarm join --token ......

sudo docker node ls

sudo docker service create --replicas  4 -p 8080:8080 --name web nginx

sudo docker service ls

sudo docker service ps web

***RUN SWARM STACK***

sudo docker stack deploy -c docker-stack.yml vis