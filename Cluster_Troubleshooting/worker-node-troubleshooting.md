# Steps for worker-node troubleshooting

Step1: Firstly go over the worker node
      
       ssh <worker-node-name>

Step 2: Check the kubelet service

       service kubelet status
       service kubelet start
Note: if service is stopped, Please start the service

Step 3: Check the logs

      journalctl -u kubelet
      kubelet configfile: /var/lib/kubelet/config.yaml

Step 4: Start the service again

       systemctl deamon-reload
       systemctl restart kubelet
