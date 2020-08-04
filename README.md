# Learning resource for Docker certified associate 2020 with Kubernetes (k8s)

##### Docker certified associate exam 2020 has changed its exam content has made some changes on exam procedires. Make sure you are aware of the changes.
##### Since docker certified associate 2020 exam also asks k8s questions as well. I have included k8s topics which will be asked in the exam.

## Make sure you are aware about the DOMC exam type and format (~ 33 DOMC question can't be changed)
#### Practice DOMC exam from here: https://sei.caveon.com/launchpad/docker-domc-practice-exam-world-geography/domc-practice
## ~13 normal MCQ question which can be changed
#### MCQs are same as previous

# Docker cCertified Associate exam format
#### Orchestration (25% of the Exam)
#### Image Creation, Management, and Registry (20% of the Exam)
#### Installation and Configuration (15% of the Exam)
#### Networking (15% of the Exam)
#### Security (15% of the Exam)
#### Storage and Volumes (10% of the Exam)

# Domain 1: Orchestration (25% of exam)
### Content may include the following:
● Complete the setup of a swarm mode cluster, with managers and worker nodes
● Describe and demonstrate how to extend the instructions to run individual containers into
running services under swarm.
● Describe the importance of quorum in a swarm cluster.
● Describe the difference between running a container and running a service.
● Interpret the output of “docker inspect” commands.
● Convert an application deployment into a stack file using a YAML compose file with "docker
stack deploy"
● Manipulate a running stack of services.
● Describe and demonstrate orchestration activities.
● Increase the number of replicas.
● Add networks, publish ports.
● Mount volumes.
● Describe and demonstrate how to run replicated and global services.
● Apply node labels to demonstrate placement of tasks.
● Describe and demonstrate how to use templates with “docker service create”.
● Identify the steps needed to troubleshoot a service not deploying.
● Describe how a Dockerized application communicates with legacy systems.
● Describe how to deploy containerized workloads as Kubernetes pods and deployments.
● Describe how to provide configuration to Kubernetes pods using configMaps and secrets.

# Domain 2: Image Creation, Management, and Registry (20% of exam)
### Content may include the following:
● Describe the use of Dockerfile.
● Describe options, such as add, copy, volumes, expose, entry point.
● Identify and display the main parts of a Dockerfile.
● Describe and demonstrate how to create an efficient image via a Dockerfile.
● Describe and demonstrate how to use CLI commands to manage images, such as list,
delete, prune, rmi.
● Describe and demonstrate how to inspect images and report specific attributes using filter
and format
● Describe and demonstrate how to tag an image. 
● Describe and demonstrate how to apply a file to create a Docker image.
● Describe and demonstrate how to display layers of a Docker image
● Describe and demonstrate how to modify an image to a single layer.
● Describe and demonstrate registry functions.
● Deploy a registry.
● Log into a registry.
● Utilize search in a registry.
● Push an image to a registry.
● Sign an image in a registry.
● Pull and delete images from a registry.

# Domain 3: Installation and Configuration (15% of exam)
### Content may include the following:
● Describe sizing requirements for installation.
● Describe and demonstrate the setup of repo, selection of a storage driver, and installation
of the Docker engine on multiple platforms.
● Describe and demonstrate configuration of logging drivers (splunk, journald, etc.).
● Describe and demonstrate how to set up swarm, configure managers, add nodes, and
setup the backup schedule.
● Describe and demonstrate how to create and manage user and teams.
● Describe and demonstrate how to configure the Docker daemon to start on boot.
● Describe and demonstrate how to use certificate-based client-server authentication to
ensure a Docker daemon has the rights to access images on a registry.
● Describe the use of namespaces, cgroups, and certificate configuration.
● Describe and interpret errors to troubleshoot installation issues without assistance.
● Describe and demonstrate the steps to deploy the Docker engine, UCP, and DTR on AWS
and on-premises in an HA configuration.
● Describe and demonstrate how to configure backups for UCP and DTR. 

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
● Describe security administration and tasks.
● Describe the process of signing an image.
● Describe default engine security.
● Describe swarm default security.
● Describe MTLS.
● Describe identity roles.
● Compare and contrast UCP workers and managers.
● Describe the process to use external certificates with UCP and DTR.
● Describe and demonstrate that an image passes a security scan.
● Describe and demonstrate how to enable Docker Content Trust.
● Describe and demonstrate how to configure RBAC with UCP.
● Describe and demonstrate how to integrate UCP with LDAP/AD. 
● Describe and demonstrate how to create UCP client bundles.

# Domain 6: Storage and Volumes (10% of exam)
### Content may include the following:
● Identify the correct graph drivers to uses with various operating systems.
● Describe and demonstrate how to configure devicemapper.
● Compare and contrast object and block storage and when they should be used.
● Describe how an application is composed of layers and where these layers reside on the
filesystem.
● Describe the use of volumes are used with Docker for persistent storage.
● Identify the steps to take to clean up unused images on a filesystem and DTR.
● Describe and demonstrate how storage can be used across cluster nodes.
● Describe how to provision persistent storage to a Kubernetes pod using persistentVolumes.
● Describe the relationship between container storage interface drivers, storageClass,
persistentVolumeClaim and volume objects in Kubernetes. 