# How docker container are seperated from one another.

Docker uses namespaces and cgroups to run a container. We will see here what these means and what are their purpose.

## Namespaces

Namespace is a linux technology which allows process to be isolated from other process in terms of resource they are using. They can be used to prevent other processes from interferring or interacting with one another.

Docker uses namespaces to isolate containers.

This allows containers to oprerate independently and securely.

Docker uses namespaces such as followings to isolates one process from another:
1. PID : Process id isolation
2. NET : Network interfaces
3. IPC : Inter-process communication
4. MNT : Filesystem mounts
5. UTS : Kernel and version identifiers
6. User namespaces : This requires special configuration. It allows container process to run as a root inside the container while mapping the user to an unprivileged user on the host.

## Cgroups

Cgroups are also called control groups. Cgroups limits resources. It cgroups limits an application to enforce rule on resource usage (e.g --memory --memory-reservation). You can limit memoory available to specific container,