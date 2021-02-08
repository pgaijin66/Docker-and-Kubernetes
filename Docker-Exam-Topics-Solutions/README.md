
### Domain 1: Orchestration (25% of exam)

### Content may include the following:
● Update image of swarm service.

```
docker swarm update --image NEW_IMAGE:TAG SERVICE_NAME
```

● Complete the setup of a swarm mode cluster, with managers and worker nodes

Set up manager node
```
ifconfig INTERFACE
docker swarm init --advertise-addr MANAGER_NODE_IP
```

Setup worker node and manager node
```
docker swarm join-token manager
docker swarm join-token worker
```

list nodes
```
docker node ls
```

● Describe and demonstrate how to extend the instructions to run individual containers into
running services under swarm.

```
docker service create --name CONTAINER_NAMW --replicas 1 IMAGE_NAME
```
Placement constraints
```
docker service create --name webserver --constraint node.label.region==blr nginx
docker service create --name webserver --constraint node.label.region~=blr nginx
```

List service running on swarm
```
docker service ls
```

List containers associated with the service
```
docker service ps SERVICE_NAME
```

● Describe the importance of quorum in a swarm cluster.

Quorum helps to maintain docker swarm state in case when you lose manager nodes

If you have 3 manager node, you can maintain quorum in case you lose 1 manager nodes.

In order to maintain quorum, you can still maintain quorum if you lose (N-1)/2 manager nodes where N is the total number of master node.

```
If the swarm loses the quorum of managers, the swarm cannot perform management tasks. If your swarm has multiple managers, always have more than two. To maintain quorum, a majority of managers must be available. An odd number of managers is recommended, because the next even number does not make the quorum easier to keep. For instance, whether you have 3 or 4 managers, you can still only lose 1 manager and maintain the quorum. If you have 5 or 6 managers, you can still only lose two.
Even if a swarm loses the quorum of managers, swarm tasks on existing worker nodes continue to run. However, swarm nodes cannot be added, updated, or removed, and new or existing tasks cannot be started, stopped, moved, or updated.

The best way to recover from losing the quorum is to bring the failed nodes back online. If you can't do that, the only way to recover from this state is to use the --force-new-cluster action from a manager node. This removes all managers except the manager the command was run from. The quorum is achieved because there is now only one manager. Promote nodes to be managers until you have the desired number of managers.

docker swarm init --force-new-cluster --advertise-addr node01:2377
```

● Describe the difference between running a container and running a service.

Container runs a single instance of image.

Service can run single or multiple inatance of image depending upon the number of replicas provided.

● Interpret the output of “docker inspect” commands.

docker inspect provides detail information about docker objects

```
docker service inspect SERVICE_NAME --pretty
docker container inspect CONTAINER_NAME --pretty
docker node inspect NODE_ID --pretty
```

● Convert an application deployment into a stack file using a YAML compose file with "docker
stack deploy"

Docker compose yaml file can be used to deploy stack into swarm

Deploy stack
```
docker stack deploy --compose-file COMPOSE_FILE.yaml STACK_NAME
docker stack deploy -c COMPOSE_FILE.yaml STACK_NAME
```

Remove stack
```
docker stack rm STACK_NAME
```

● Manipulate a running stack of services.

Show all list of running service]
```
docker stack services STACK_NAME
```

Filtering
```
docker stack services --filter name=SERVICE1_NAME --filter STACK_NAME
```

Formating
```
docker stack services --format "{{.ID}}: {{.Mode}} {{.Replicas}}"
```

● Describe and demonstrate orchestration activities.

THere are different orchestation activities in swarm:
1. Deploy stack
2. Create service
3. Scale service
4. Remove service
5. remove stack

● Increase the number of replicas.

```
docker service scale SERVICE_NAME=3
docker service update --replicas=4 SERVICE_NAME
```

● Add networks, publish ports.

Add networks
```
docker network create my-net
docker network create --driver overlay NETWORK_NAME
docker network create --driver overlay --attachable NETWORK_NAME 
docker network create --driver bridge NETWORK_NAME
docker network create --driver maclvan --subnet 192.168.0.0/24 --gateway 192.168.0.1 -o parent=eth0 NETWORK_NAME
```

Create container and add network to container
```
docker container run -d --rm --name busybox --network NETWORK_NAME radian/busyboxplus
docker container run -d --rm --name nginx --net host nginx
```

Connect my-nginx container to network
```
docker network ls
docker network connect NETWORK_NAME nginx
```

Disconnect a running container from an existing network
```
docker network disconnect NETWORK_NAME CONTAINER_NAME
```

Inspect network
```
docker network inspect NETWORK_NAME
```

Delete network
```
docker network rm NETWORK_NAME
```

Publish port 
```
docker container run --it --name nginx -p 8080:80 nginx
```

List port mapping of container
```
docker port CONAINTER_ID
```

Display port
```
docker ps
```
● Drain swarm node and make it active.

```
docker node update --availability drain NODE_NAME
docker node update --availability active NODE_NAME
```

● Mount volumes.

Creating volume
```
docker volume create VOLUME_NAME
```

Get information baout volume
```
docker volume inspect VOLUME_NAME
```

Deleting volume
```
docker volume rm VOLUME_NAME
```

```
docker container run -dt --name mynginx -v /root/index:/usr/share/nginx/html nginx
```
```
docker container run -dt --name mynginx --mount type=bind,source=/root/index,target=/usr/share/nginx/html nginx
```

```
docker container run -dt --name mynginx --mount type=volume,source=my-volume,target=/usr/share/nginx/html nginx
```

● Describe and demonstrate how to run replicated and global services.
```
docker service create --name SERVICE_NAME --mode=global IMAGE

Replicated service will hav N number of containers defined with the --replica flag

Global service will create 1 container in every node of the cluster.
```


● Apply node labels to demonstrate placement of tasks.
```
docker node update --label-add LABEL=VALUE NODE
```

```
docker node ls
```

Adding lable to node
```
docker node update --label-add LABEL=VALUE NODE
docker node update --label-add region=sydney WORKER_NODE_ID
```

```
docker node update --label-add availability_zone=east <NODE_NAME>
docker node update --label-add availability_zone=west <NODE_NAME>
```

```
 docker node update --label-add availability_zone=west node2
 docker node update --label-add availability_zone=west node2
```

View existing node lables
```
docker node inspect --pretty NODE
```

```

# apply node label (key w/no value or type)
docker node update --label-add foo --label-add bar worker1
docker node update --label-add type=queue worker1

# placement of service using node
docker service create \
--replicas 9 \
--name redis_2 \
--placement-pref 'spread=node.labels.datacenter' \
--placement-pref 'spread=node.labels.rack' \ redis:3.0.6
```

● Describe and demonstrate how to use templates with “docker service create”.
It is use to provide dynamic values during service creation. Templates can be used to give somewhat dynamic values to some flags with docker service create

--hostname
--env
--mount

```
# pass in --hostname, --mount, or --env & use Go syntax
docker service create --name hosttempl \
--hostname="{{.Node.Hostname}}-{{.Node.ID}}-{{.Service.Name}}" \
busybox top
```

```
docker service create --name node-hostname --env NODE_HOSTNAME="{{.Node.Hostname}}" --replicas 3 nginx
```


● Identify the steps needed to troubleshoot a service not deploying.

Use docker system events to get real time information from your server.
```
First of all, make sure at least one node is not paused or drained,

If there is enough memory available to meet the value defined in the service

If check the service constraints defined (could make a service be in "pending" state).

Then get a task ID with
docker service ps <service-name>.

Next, it is useful to check meta data with
docker inspect <task-id>

In particular, Error message before container start is in the status field, and then reconfirm whether it was started with the intended parameters.

If the task has container ID, it was abnormally exited after starting the container, so check the log of the container with
docker logs <container-id>
```


● Describe how a Dockerized application communicates with legacy systems.
```
Using container name port, network, ENDS (container name and alias)
```
```
# publish ports via --publish or -p to map container port to host port
-p 8080:80 #80 for container & 8080 for host

# attach it to a network on creation or after
--network
docker network connect

# inherits DNS of docker daemon unless you set
--dns
--dns-search
--dns-opt
```


● Describe how to deploy containerized workloads as Kubernetes pods and deployments.

```
kubectl run nginx --image=nginx
kubectl exec --it mywebserver -- /bin/bash
kubectl delete pod POD_NAME
kubectl get pods --show-labels
kubectl delete rs REPLICASET_NAME
kubectl descibe describe deployments nginx-deployment
kubectl rollout history deployment.v1.apps/nginx-deployment
kubectl rollout history deployment.v1.apps/nginx-deployment --revision 1
kubectl create secret TYPE NAME DATA
kubectl create secret generic firstsecret --from-literal=dbpass=mypassword123
kubectl get secrets
kubectl create configmap dev-config --from-lliteral=app.mem-2068
kubectl create configmap dev-properties --from-file=dev.properties
kubectl describe configmap dev-config    

```

Pod
```
apiVersion: v1
kind: Pod
metadata:
  name: nginxwebserver
  labels:
    name: nginxwebserver
spec:
  containers:
  - name: myapp
    image: nginx
    resources:
      limits:
        memory: "128Mi"
        cpu: "500m"
```

Replica set
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  # Selector should match the labels associated with pods
  selector:
    matchLabels:
    # This is for replica set to count how many replicas of replicaset is running
      tier: frontend
  # Using template you do not need to specify apiVersion and Kind
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
```

Deployments
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

Service
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  ports:
  - port: 8080
    targetPort: 80
```

Endpoints
```
apiVersion: v1
kind: Endpoints
metadata:
  name: nginx-service
subsets:
  - addresses:
      - ip: 10.244.0.32
      - ip: 10.244.0.241
    ports:
      - port: 80
```

Service and Nodeport
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx-app
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx
        image: nginx:1.13.12
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx-app
  name: nginx-svc
  namespace: default
spec:
  type: ClusterIP  # use ClusterIP as type here Can use NodePort
  ports:
    - port: 80
  selector:
    app: nginx-app
```

DaemonSet
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      # this toleration is to have the daemonset runnable on master nodes
      # remove it if your masters can't run pods
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd-elasticsearch
        image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

● Resource requests and limits
THis is use to specify the resource limitation while creating pods on specific nodes.

This also checks which nodes has enough capacity to run the application.

These are two ways in whih we can control the amount fo resource that can be assigned to a pod (CPU, Memory)

Request: Resource Guaranteed to pod
Limits: Makes sure that container does not take node resources above a specific value.
```
resources:
	requests:
		memory: "64Mi"
		cpu: "0.5"
	limits:
		memory: "128Mi"
		cpu: "1"
```

Check resource limits
```
kubectl describe node NODENAME
```
Scheduler
k8s scheduler decides the ideal node to run the pod depending on the requests and limits.

If your pod requires 8GN of RAM, however there are no ndoes within your cluster which has 8GB RAM, then your pod will never get scheduled and pod will go to PENDING state.

If you check logs of pod, it will show you insufficient cpu.

● Labels and Selectors
Labels are key/value pairs that are attached to objects, such as pods.
name: database env: prod

Selectors allows us to filters objects based on labels
Labels and selectors in k8s
Add labels to pods to define which environment they are from.

List pods on only dev environment
```
kubectl get pods -l env=dev
```
Adding label to pod
```
kubectl label pods POD1_NAME env=dev
kubectl label pods POD2_NAME env=prod
```
env=dev is the label

Show labels
```
kubectl get pods --show-labels
```
We add labels to k8s object in metadata section.

● Taints and tolerations

Taints
Taints are used to repel the pods from a specific node. If taint has been applied to node, and pod is scheduled to worker node, it will not the pod on tainted worker node.

Check the taint flag on the output
```
kubectl describe WORKERNODE
```
Apply taint so that pods are not scheduled in the tainted worker node.
```
kubectl taint nodes NODENAME key=value:NoSchedule
```
Toleration
In order to pass pods on the tainted worker node, we need to have a pass. This is called toleration

This pass is called Toleration

You pass toleration to the yaml file when we create a deployment file with key, operator and effect,
```
tolerations:
- key: "key"
  operator: "Exists"
  effect: "NoSchedule"
```

● Describe how to provide configuration to Kubernetes pods using configMaps and secrets.

ConfigMap
```
apiVersion: v1
kind: Pod
metadata:
  name: config-pod
spec:
  containers:
  - name: config-pod
    image: nginx:alpine
    ports:
      - containerPort: 80
    volumeMounts:
      - name: config-pod
        mountPath: /etc/config
  volumes:
  - name: config-pod
    configMap:
      name: dev-properties
```


Secret
```
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  # Username and password should be bas64 encoded
  username: ZGJhZG1pbg==
  password: MTIzNDUK
```


# Domain 2: Image Creation, Management, and Registry (20% of exam)
### Content may include the following:

● Immutable in DTR

Enable immutable set to repository > setting and add immutability

● DTR cache

Used to decrease amount of time to pull image. You can set up DTR cache at different location

● DTR garbage collection

Can configure DTR to automatically deleted unused images layers thus saving disk space

● DTR HA

Scale horizontally
Make use of centralized storage

For DTR = 3 dedicated nodes
For UCP = 3 dedicated nodes

● Oschestrator types of UDP

UCP uses swarm and k8s

from cli
```
docker node update --label-add com.docker.ucp.orchestrator.kubernetes=true NODE_ID
```

● Storage driver in DTR

By default DTR stores in filesyste, can configure to store in external storage backend.

● Supported storage systems in DTR

NFS
BIND mount 
volume
S3
Google cloud

● DTR web hook

Con configure DTR to automatically post events notifications to webhook URL of choosing

● Accessing insecure registry.

By default docker will not allow you to perform operation with insecure registry. You can override by adding following to /etc/docker/daemon.json

```
{
    ""insecure-registries:["myregistry.com:5000"]
}
```

● Describe the use of Dockerfile.

```
docker build -t myimage .
docker tag IMAGE_NAME:TAG IMAGE_ID
```

● Describe options, such as add, copy, volumes, expose, entry point.

```
ADD and COPY : Add tar and url 
VOLUME : mount volume
EXPOSE: Intent to use port
ENTRYPOINT: It helps to make container executable

```

● Identify and display the main parts of a Dockerfile.

```
docker image history IMAGE_ID

FROM Sets the Base Image for subsequent instructions build stage (must be first line and at least once).
RUN execute any commands in a new layer on top of the current image and commit the results.
CMD provide defaults for an executing container.
EXPOSE informs Docker that the container listens on the specified network ports at runtime. NOTE: does not actually make ports accessible.
ENV sets environment variable.
ADD copies new files, directories or remote file to container. Invalidates caches. Avoid ADD and use COPY instead.
COPY copies new files or directories to container. Note that this only copies as root, so you have to chown manually regardless of your USER / WORKDIR setting.
ENTRYPOINT configures a container that will run as an executable.
VOLUME creates a mount point for externally mounted volumes or other containers.
USER sets the user name for following RUN / CMD / ENTRYPOINT commands.
WORKDIR sets the working directory.
ARG defines a build-time variable.
ONBUILD adds a trigger instruction when the image is used as the base for another build.
STOPSIGNAL sets the system call signal that will be sent to the container to exit.
LABEL apply key/value metadata to your images, containers, or daemons.
```

● Describe and demonstrate how to create an efficient image via a Dockerfile.

```
Multi-stage build

Should generate containers that are as ephemeral as possible. Image should be as small as possible. Build context should be within a given folder. String RUN commands together with backslashes.
```

● Describe and demonstrate how to use CLI commands to manage images, such as list,
delete, prune, rmi.

```
docker image ls
docker image rm IMAGE_ID
docker image prune -a
```

● Describe and demonstrate how to inspect images and report specific attributes using filter
and format


● Describe and demonstrate how to tag an image.

```
docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

● Describe and demonstrate how to apply a file to create a Docker image.

● Describe and demonstrate how to display layers of a Docker image

```
docker image history IMAGE_NAME
docker inspect 
```

● Describe and demonstrate how to modify an image to a single layer.

```
docker export CONTAINER_NAME | docker import - IMAGE_NAME
```

```
#Without rebuilding
-create a container from the image: docker create
-export the container to a flattened tar: docker container export
-load the image back: docker image import

#With rebuilding
See the --squash option of docker build
```

● Describe and demonstrate registry functions.

● Deploy a registry.

```
Docker Trusted Registry (DTR) is a commercial product that enables complete image management workflow, featuring LDAP integration, image signing, security scanning, and integration with Universal Control Plane. DTR is offered as an add-on to Docker Enterprise subscriptions of Standard or higher.

docker run -d -p 5000:5000 --name registry registry:2
```

● Log into a registry.

```
docker login REGISTRY_URL
```

● Utilize search in a registry.

```
docker search IMAGE_NAME

docker search --filter "is-official=true" --filter "stars=3" busybox
```

● Push an image to a registry.

First tag image with the DNS name of the registry then push
```
docker push REPOSITORY/IMAGE:TAG

docker image push [OPTIONS] NAME[:TAG]
# NAME including registry endpoint (also built with it in the name)
```

● Sign an image in a registry.

```
DCT = Docker Content Trust

export DOCKER_CONTENT_TRUST=1
docker push <dtr-domain>/<repository>/<image>:<tag>
```

● Pull and delete images from a registry.

```
docker login REGISTRY_URL
docker pull IMAGE:TAG

You can delete an image from the Docker Hub by deleting individual tags in your repository in the tags area.
```

# Domain 3: Installation and Configuration (15% of exam)
### Content may include the following:
● Describe sizing requirements for installation.

● Describe and demonstrate the setup of repo, selection of a storage driver, and installation of the Docker engine on multiple platforms.

● Describe and demonstrate configuration of logging drivers (splunk, journald, etc.).

```
{
  "storage-driver": "aufs"
}
```
You can use log drive using flags --log-driver and --log-opt
OR
/etc/docker/daemon.json
```
{
	"log-driver":"json-file",
	"log-opts":{
		"max-size":"15m"
	}
}
```
● Describe and demonstrate how to set up swarm, configure managers, add nodes, and
setup the backup schedule.

Backup swarm
Stop docker
backup content of /var/lib/docker/swarm
start docker


● Describe and demonstrate how to create and manage user and teams.

● Describe and demonstrate how to configure the Docker daemon to start on boot.

```
systemctl enable dockerd
```
● Describe and demonstrate how to use certificate-based client-server authentication to
ensure a Docker daemon has the rights to access images on a registry.


● Describe the use of namespaces, cgroups, and certificate configuration.
USR name space is not enabled by default.

```
Namespaces
MNT
UTS
IPC
USR
NET

Cgroups
docker container run -dt --name constraint01 --cpus=1.5 busybox sh
docker container run -dt --name constraint02 --cpuset-cpus=0,1 busybox sh
```
Limiting CPI
```
--cpus=<value> number of CPU for container
--cpuset-cpus= , limit specific CPU or cores a container can use
```
● Describe and interpret errors to troubleshoot installation issues without assistance.

● Describe and demonstrate the steps to deploy the Docker engine, UCP, and DTR on AWS
and on-premises in an HA configuration.

● Describe and demonstrate how to configure backups for UCP and DTR. 

DTR backup does not backup things related to users/organizations/team

DTR backups raft keys, membershio, services, networks, configs, secrets

```
use backup command. Run backup DTR with backup command

DTR backup backs up configuration and image metadata. It does not backup user and organizations. It also does not backup docker images stored in your registry.

Users and organizations can be backup during UCP backup. 
```
```
docker run --log-driver none -i --rm docker/dtr backup --ucp-url https://172.31.40.237 -ucp-insecure-tls --ucp-username admin --ucp-password YOUR-PASSWORD-HERE > backup.tar
```


# Domain 4: Networking (15% of exam)
### Content may include the following:
● Describe the Container Network Model and how it interfaces with the Docker engine and
network and IPAM drivers.
● Describe the different types and use cases for the built-in network drivers.
● Describe the types of traffic that flow between the Docker engine, registry and UCP
controllers.
● Describe and demonstrate how to create a Docker bridge network for developers to use for
their containers.
● Describe and demonstrate how to publish a port so that an application is accessible
externally.
● Identify which IP and port a container is externally accessible on.
● Compare and contrast “host” and “ingress” publishing modes.
● Describe and demonstrate how to configure Docker to use external DNS.
● Describe and demonstrate how to use Docker to load balance HTTP/HTTPs traffic to an
application (Configure L7 load balancing with Docker EE).
● Understand and describe the types of traffic that flow between the Docker engine, registry,
and UCP controllers
● Describe and demonstrate how to deploy a service on a Docker overlay network.
● Describe and demonstrate how to troubleshoot container and engine logs to resolve
connectivity issues between containers.
● Describe how to route traffic to Kubernetes pods using ClusterIP and NodePort services.
● Describe the Kubertnetes’ container network model.

# Domain 5: Security (15% of exam)
### Content may include the following:

● Secure communication between container using IPSEC tunnel

```
docker network create --opt encrypted --driver overlay 
docker network create -o encrypted --driver overlay
```

● Describe security administration and tasks.

● Describe the process of signing an image.

```
DCT
```

● Describe default engine security.

● Describe swarm default security.

● Describe MTLS.

● Describe identity roles.

● Compare and contrast UCP workers and managers.

● What is service routing mesh ?

```
Even if the container is not running on host, you can access the service from node ip and port.
```

● Describe the process to use external certificates with UCP and DTR.

```
--external-ca flag of docker swarm init
```

● Describe and demonstrate that an image passes a security scan.

● Describe and demonstrate how to enable Docker Content Trust.

```
DOCKER_CONTENT_TRUST=1
```

● Describe and demonstrate how to configure RBAC with UCP.

```
Subject, roles and collections
Subject container user, team and organization.
Roles containers w?R access
Collections are the docker objects like nodes, swarm, service etc
```

● Describe and demonstrate how to integrate UCP with LDAP/AD. 

```
LDAP for central authentication

In UCP admin, under authentication/authorization you can enable LDAP with LDAP creds.
```

● Describe and demonstrate how to create UCP client bundles.

```
A client bundle is a groupd of certificates downloadeable directly from docker unversal control plane (UCP)

Create in UCP using laptop
Login to remove container from your laptop without SSH (via API)

Create Client Bundle in UCP and download it

Copy client bundle to the container
```

# Domain 6: Storage and Volumes (10% of exam)
### Content may include the following:
● Identify the correct graph drivers to uses with various operating systems.

● Describe and demonstrate how to configure devicemapper.

```
Stop docker service

edit /etc/docker/daemon.json

{
  "storage-driver": "devicemapper"
}
```

● Compare and contrast object and block storage and when they should be used.

Object storage: overlay and aufs

Block storage: zfs, btrfs

● Describe how an application is composed of layers and where these layers reside on the
filesystem.

● Describe the use of volumes are used with Docker for persistent storage.

```
docker container run -dt --name mynginx -v /root/index:/usr/share/nginx/html nginx

docker container run -dt --name mynginx --mount type=bind,source=/root/index,target=/usr/share/nginx/html nginx
```

● Identify the steps to take to clean up unused images on a filesystem and DTR.
● Describe and demonstrate how storage can be used across cluster nodes.

● Describe how to provision persistent storage to a Kubernetes pod using persistentVolumes.

On disk file in caontianer are ephemeral

k8s supports EBSm cinder, glusterfs, local, nfs etc as volume

hostPath mounts directory from the hosts directory to pod

```
apiVersion: v1
kind: Pod
metadata:
  name: demopod-volume
spec:
  containers:
  - image: nginx
    name: test-container
    volumeMounts:
    - mountPath: /data
      name: first-volume
  volumes:
  - name: first-volume
    hostPath:
      path: /mydata
      type: Directory
```

```
kubectl apply -f <yaml>
kubectl exec -it POD_NAME bash 
```

● Describe the relationship between container storage interface drivers, storageClass,
persistentVolumeClaim and volume objects in Kubernetes. 

Persistent Volume (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage class.

Every volume which is created can be of different types.

This can be taken care by the storage administrator or OPS Team.


eg: 
volume 1 size: small speed: fast
Voume 2 size: med speed: Flast

Persistent Volume Claim (PVC) is the request for the storage by user.

Within the claim, user need to specify the size of the volume along with access mode.

PV and PVC


1. SA create PV (pv.yaml)
```
apiVersion: v1
kind: persistentVolume
metadata:
  name: block-pv
spec:
  StorageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /tmp/data
```

2. Deverloper can raise a claim (i want a specific types of PV) (pvc.yaml)
```
apiVersion: v1
kind: persistentVolumeClaim
metadata:
  name: pvc
spec:
  StorageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

```
kubectl apply -f pvc.yaml
kubectl get pvc
```

3. Reference that clain with the PodSpec file.

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-pvc
spec:
  containers:
     - name: app
       image: nginx
       volumeMounts:
       - mountPath: /data
         name: my-volume
  volumes:
    - name: my-volume
      persistentVolumeClaim:
        claimName: pvcs
```
