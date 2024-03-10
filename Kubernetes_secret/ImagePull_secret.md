# ImagePull Secret

<h4> <b> How to create imagepullsecret:</b></h4>
command: 

      kubectl create secret docker-registry secret-name --docker-username=username --docker-password=docker_password --docker-server=myprivateregistry.com --docker-email=dock_user@myprivateregistry.com

<h4><b> How to configure in pod yaml file</b></h4>

   ---
     apiVersion: v1
   
     kind: Pod
   
     metadata:
   
        name: mypod
   
     spec:
   
       containers:
     
        - name: container
        
          image: <registry-username>/<image-name>:<tag>
    
       imagePullSecrets:
     
       - name: myregistrykey
