**Pod:** 
The pod is the smallest unit in the Kubernetes cluster.
The pod is a collection of containers or maybe one container running inside it.
It is a single instance of an application, if required to deploy another application create a new pod with a containerized application.
It is the best practice to create one container inside one pod, sometimes there is a dependency of our application on another pod(called helper container), In that case, we can create two containers inside one pod.
Each pod gets one internal IP address to communicate with each other (virtual network created by k8s).
If the pod is restarted IP address may change.

**Pod’s lifecycle:**
A pod passes through several phases in its lifecycle.
Pending
Running
Succeeded
Failed
ImagePullBackoff
CrashloopBackoff

**Pod Scheduling Cycle:**
The scheduler assigns a node to the pod and then the containers start within the pod on the designated node.

**Can a Pod have multiple IP addresses?**
No, a Pod in Kubernetes has a single IP address. However, each container within the Pod shares this same IP address and is accessible over the same network.

**For More Knowledge:**
Document_Link: https://devunnatig.hashnode.dev/launching-your-first-kubernetes-cluster-with-nginx-running
