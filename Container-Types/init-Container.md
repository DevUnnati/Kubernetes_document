InitContainers:
Init Containers are specialized container that run before app containers in a Pod.
Init Containers can contain utilities or setup scipts not present in app image.

SideCar Containers:
Sidecar container is a container that starts before the main application container and continues to run.

Features of InitContainers:
a) Init container always run to completion.
b) Each init container must complete successfully before the next one starts.
c) If a pod's init container fails, the kubelet repeatedly restarts that init container until it succeeds.
d) However if a pod has a restartPolicy of Never, and an initcontainer fails during startup of that pod.

Init container present inside the spec section and configuration like normal conatiner except you have to mention this is initconatiner.
