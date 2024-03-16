# Steps For Fixing the kubelet

Step1: Check node status
    
     kubectl get node

Step2: Go over the workernode (because kubelet always run on worker node).
    
    ssh cluster3-node1

Step3: Grep the kubelet process
   
     ps -aux | grep kubelet

Step4: Check kubelet service status
   
     service kubelet status

Step5: Find out the kubelete config file

     whereis kubelet or
     journalctl -u kubelet

Step6: After fixing the issue don't forget to run these lines

     systemctl daemon-reload
     service kubelet restart
     service kubelet status 
