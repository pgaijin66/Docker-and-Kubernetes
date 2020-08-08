## Resource requests and limits

THis is use to specify the resource limitation while creating pods on specific nodes.

This also checks which nodes has enough capacity to run the application.

These are two ways in whih we can control the amount fo resource that can be assigned to a pod (CPU, Memory)

### Request: Resource Guaranteed to pod
### Limits: Makes sure that container does not take node resources above a specific value.

```
resources:
	requests:
		memory: "64Mi"
		cpu: "0.5"
	limits:
		memory: "128Mi"
		cpu: "1"
```

### Check resource limits
```
kubectl describe node NODENAME
```

# Scheduler

k8s scheduler decides the ideal node to run the pod depending on the requests and limits.

If your pod requires 8GN of RAM, however there are no ndoes within your cluster which has 8GB RAM, then your pod will never get scheduled and pod will go to PENDING state.

If you check logs of pod, it will show you insufficient cpu.