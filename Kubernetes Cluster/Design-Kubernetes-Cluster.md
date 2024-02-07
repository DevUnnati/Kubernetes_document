# Design and Install a Kubernetes Cluster

1) What is the purpose of this cluster? --> is it for learning or development or testing purpose and Hosting Production with Application?
2) 
3) What kind of workloads are you going to run on this cluster?
4) 
5) How many application are to be hosted on this cluster?
6) 
7) What kind of application are going to be hosted? --> big data analytics or Java Application
8) 
9) What type of traffic from this application are expecting --> heavy traffic or normal traffic

Education:

a) Minikube

b) Single Node Cluster with kubeadm

Development and testing:

Multi-node cluster with a Single Master and Multiple Worker

Setup using kubeadm tool or quick provision on GCP or AWS or AKS

Production level Cluster:

High Availability MultiNode cluster with multiple master nodes

Kubeadm or GCP or Kops on AWS or other supported platforms

Upto 5000 nodes

Upto 150000 Pods in the Cluster

Upto 300000 Total Containers

Upto 100 Pods per node

b) Cloud or OnPrem?

In both type of cluster, it's available.

For OnPrem Kubeadmin is very useful tool.

Storage:

a) High Performance - SSD Backend Storage

b) Multiple Concurrent Connections: Network based Storage

c) Persistent Shared Volume for shaed access across multiple pods

d) Label nodes with specific disk types

e) USe Node Selectors to assign applicatiobs to Node with Specific disk types


Nodes:

Virtual or physical Machines

Minimum of 4 node cluster (size based on workload)

Master vs Worker Nodes

Linux X86_64 Architecture

Note: Master Nodes also Consider as workload but in production it's good to not consider masternode as workload.
You may choose the separate the etcd clusters from the master node to different worker nodes.

c) Choosing Kubernetes Infrastructre:

In case of windows you can't deploy kubernetes cluster easily because windows binaries are missing.
You must rely on virtualization Software like Hyper-V or VMWare WorkStation or VirtualBox to create Linux VMs on which you can run Kubernetes

Kubeadm:

a)Kubeadm is used to deploy single node or multi node cluster very quick.

 You  must provision supported host with supported configuration yourself.
 
b) Kubeadm requires vm to be ready but minikube and other one deploy VMs.


For Turnkey Solutions:

a) Openshift

Openshift is a popular on-prem kubernetes Platform by Redhat.

Openshift is an open source container application platform and is built on top of kubernetes.

It provides set of additional tools and a nice GUI to create and manage kubernetes constructs and easily integrate with CI/CD pipelibe etc.

b) Cloud Foundry Container Runtime:

Cloud Foundry Container Runtime is an open source project from Cloud Foundry that helps in deploying and managing highly available kubernetes cluster using thier open source tool called BOSH.

c) VMWare Cloud PKS Solution: You can use this also.

d) Vagrant provides a set of useful scripts to deploy a kubernetes cluster on different cloud service providers.

Hosted Solution:

Google Container Engine

Openshift Online

Azure Kubernetes Service

Amazon Elastic Container Service for Kubernetes

VirtualBox

one master node

two worker node


d) High Availability in Kubernetes:

In High Availability Production envirnoment we consider atleast two masternode.

etcd-set up have two type of topology ---> stacked ETCD Topology and External ETCD Toplogy.

To reach the external ETCD server --> do the entry in kubeapiserver.yaml

e) ETCD in HA:
