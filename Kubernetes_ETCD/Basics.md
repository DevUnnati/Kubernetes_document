# ETCD-Cluster:

<b><h3> Prerequisites:</h3></b>

a) No resource starvation occurs over the cluster.

b) Any resource starvation can lead to heartbeat timeout, causing instability of cluster.

c) To overcome the above problem, run the etcd cluster on dedicated machines or isolated env.

d) In production etcd versions are --> 3.4.22+ and 3.5.6+


<b><h3>Resource Requirements:</h3></b>
https://etcd.io/docs/current/op-guide/hardware/#example-hardware-configurations

<h3><b> Cluster Types: </h3></b>
  
Single-Node ETCD Cluster
  
Multi-Node ETCD Cluster

<h3><b> Securing ETCD Cluster:</h3></b>
Have Permission from ETCD Cluster is equivalent to root permission, so we are careful before giving access. Only API Server should have access to it.
  
a) For securing ETCD Cluster--->
     i) SetUP Firewall Rules
	 ii) Use the Security Features Provided by ETCD.
	 
b) ETCD Security Features depend on x509 Public Key Infrastructure(PKI).

c) Key pairs(peer.key) and (peer.cert) ---> used to secure communication between etcd members.

d) client.key and client.crt ---> used to secure communication between etcd and its client.

<h3><b>To generate certificate follow this:</h3></b>
https://github.com/etcd-io/etcd/blob/main/hack/tls-setup/config/req-csr.json



<h3><b>Securing Communication:</b></h3>

ETCDCTL_API=3 etcdctl --endpoints 10.2.0.9:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  member list
  
<h3><b>Limiting Access of ETCD Cluster:</b></h3>
After securing the communication, restrict the access of the etcd cluster to only the Kubernetes API Servers.
for that USE TLS Authentication.

i)For giving access to Kubernetes API configure it by 
   a) k8sclient.key 
   and b) k8sclient.cert
   because these are trusted by etcd.ca.
   
ii) --trusted-ca-file: verifies the certificate from clients by using system CAs or the CA passed.

iii) --client-cert-auth=true and --trusted-ca-file=etcd.ca will restrict the access to clients with the certificate k8sclient.cert.


**Note:** etcd authentication is not currently supported by Kubernetes.

<h3><b>Replacing a failed etcd member:</b></h3>
a) To improve the health of the cluster, etcd replace failed members immediately.

b) If multiple members fail, replace them one by one.

c) For replacing failed member requires two steps
    i) Remove failed member
	ii) Add new member
 
d) If you have three members like
    i) ip1
	ii) ip2
	iii) ip3
	so if the first member fails, it is replaced by ip4.
	
<h5><b>get the member ID of the failed member:</b></h5>
_etcdctl --endpoints=http://10.0.0.2,http://10.0.0.3 member list
_
e) Do either of the following:
   i) If each Kubernetes API server is configured to communicate with all etcd members, remove the failed member from the --etcd-servers flag, then restart each Kubernetes API server.
   
   ii)   If each Kubernetes API server communicates with a single etcd member, then stop the Kubernetes API server that communicates with the failed etcd.
   
f) Stop the etcd server on the broken node. It is possible that other clients besides the Kubernetes API server is causing traffic to etcd and it is desirable to stop all traffic to prevent writes to the data dir.


<h5><b>Remove the failed member from the ETCD Server:</b></h5>
_etcdctl member remove <member-id>
_
<h5><b>Add the new member:</b></h5>
__etcdctl member add member4 --peer-urls=http://10.0.0.4:2380_
  
Start the newly added member on a machine with the IP 10.0.0.4:

export ETCD_NAME="member4"
export ETCD_INITIAL_CLUSTER="member2=http://10.0.0.2:2380,member3=http://10.0.0.3:2380,member4=http://10.0.0.4:2380"
export ETCD_INITIAL_CLUSTER_STATE=existing
etcd [flags]

Do either of the following:
  i)  If each Kubernetes API server is configured to communicate with all etcd members, add the newly added member to the --etcd-servers flag, then restart each Kubernetes API server.
  
  ii) If each Kubernetes API server communicates with a single etcd member, start the Kubernetes API server that was stopped in step 2. Then configure Kubernetes API server clients to again route requests to the Kubernetes API server that was stopped. This can often be done by configuring a load balancer.
  
  
<h3><b>Backing up an etcd cluster:</b></h3>
** a) Built-in snapshot:**
     
	Command for taking Snapshot or backup:
     _ETCDCTL_API=3 etcdctl --endpoints $ENDPOINT snapshot save snapshot.db_
     
	Verify the snapshot:
     _ETCDCTL_API=3 etcdctl --write-out=table snapshot status snapshot.db
_ 
 **b) Volume Snapshot:**
     If etcd is running on a storage volume that supports backup, such as Amazon Elastic Block Store, back up etcd data by taking a snapshot of the storage volume.
	 
	 Command for taking snapshots:
	 ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
    --cacert=<trusted-ca-file> --cert=<cert-file> --key=<key-file> \
    snapshot save <backup-file-location>
	 
  

<h3><b>Scaling out etcd clusters:</b></h3>

A reasonable scaling is to upgrade a three-member cluster to a five-member one, when more reliability is desired. See etcd reconfiguration documentation for information on 
how to add members to an existing cluster

<h3><b>Restoring an etcd cluster:</b></h3>

Restoring a version from a different patch version of etcd also is supported.

<h5><b>Command for Restoring:</b></h5>

_ETCDCTL_API=3 etcdctl --endpoints 10.2.0.9:2379 snapshot restore snapshot.db_

<h5><b>Command for data-directory location:</b></h5>

_ETCDCTL_API=3 etcdctl --data-dir <data-dir-location> snapshot restore snapshot.db_


_Export ETCDCTL envirnoment variable:
export ETCDCTL_API=3
etcdctl --data-dir <data-dir-location> snapshot restore snapshot.db
_


If the access URLs of the restored cluster are changed from the previous cluster, the Kubernetes API server must be reconfigured accordingly. In this case, restart Kubernetes API servers with the flag --etcd-servers=$NEW_ETCD_CLUSTER instead of the flag --etcd-servers=$OLD_ETCD_CLUSTER. Replace $NEW_ETCD_CLUSTER and $OLD_ETCD_CLUSTER with the respective IP addresses. If a load balancer is used in front of an etcd cluster, you might need to update the load balancer instead.


If the majority of etcd members have permanently failed, the etcd cluster is considered failed. In this scenario, Kubernetes cannot make any changes to its current state. Although the scheduled pods might continue to run, no new pods can be scheduled. In such cases, recover the etcd cluster and potentially reconfigure Kubernetes API servers to fix the issue.


**Note:**
If any API servers are running in your cluster, you should not attempt to restore instances of etcd. Instead, follow these steps to restore etcd:

stop all API server instances
restore state in all etcd instances
restart all API server instances
We also recommend restarting any components (e.g. kube-scheduler, kube-controller-manager, kubelet) to ensure that they don't rely on some stale data. Note that in practice, the restore takes a bit of time. During the restoration, critical components will lose leader lock and restart themselves.
