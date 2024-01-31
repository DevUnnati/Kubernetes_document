# Upgrading Cluster 

Upgrade Control Plane

Step 1: Determine which version to upgrade to 

Step 2: Upgrading Control Plane Nodes

   i) upgrade kubeadm:
   
        apt-get upgrade -y kubeadm=1.12.0-00
	      kubeadm upgrade apply v1.12.0
       
   ii) Verify that downloads worker
   
          kubeadm version
          
   iii) Verify the upgrade plan
   
         kubeadm upgrade plan

  Step 3: Drain the Node
  
        kubectl drain <node-to-darin> 
          
	Step 4: Upgrade the kubeletes
 
         apt-get upgrade -y kubelete=1.12.0-00
         
         systemctl restart kubelete
         
  Step 5: Uncordon the Node 
         
		   kubectl uncordon <node-name>

Upgrade the Worker Node

Steo 1: Call kubeadm upgrade:

          kubeadm upgrade node

Step 2: Drain the node:

         kubectl drain <node-to-darin> --ignore-daemonsets
         
Step 3: Upgrade the kubelet

         apt-get upgrade -y kubelete=1.12.0-00
         
         kubeadm upgrade node config --kubelete-version v1.12.0
         
Step 4: Uncordon the node

         kubectl uncordon <node-to-uncordon>
  


