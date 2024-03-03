<h2> Install NGINX Ingress Controller </h2>

Step1: Install helm repo of nginx controller

    helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

Step2: Update helm repo

    helm repo update
  
Step 3: Install NGINX Ingress Controller

     helm install my-nginx-ingress ingress-nginx/ingress-nginx --version 4.0.6

Step 4: Custom Values.yaml (optional)
  
     controller:
       extraArgs:
        update-status: "false"

Step 5: Install NGINX Ingress controller with custom values.yaml

     helm install my-nginx-ingress ingress-nginx/ingress-nginx -f values.yaml

Step 6: helm upgrage NGINX Ingress Controller

     helm upgrade my-nginx-ingress ingress-nginx/ingress-nginx -f values.yaml


