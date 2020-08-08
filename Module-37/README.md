# Liveness Probe

If applicaiton is broker, liveness probe helps to check whether the pod is alive or not and restart it if not running.

k8s provides livenss probes to detect and remedy such situations.

Automates restart if app is in broken state.

Can check liveness probe via command, HTTP, TCP

# Readiness Probe

If application is running but unavailable to serve traffic, we do not want to kill the pod however we also do not want it to server the traffic.

Can write a script or value to verify that pod is ready ot server the incoming traffic.