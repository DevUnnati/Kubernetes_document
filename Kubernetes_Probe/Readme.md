<h1> <b> Kubernetes Probes</b> </h1>

<p> This repository houses a Kubernetes application with implemented health and readiness probes, crucial for Kubernetes to assess the containerized application's state.
To deploy the application, use the provided deployment.yaml file with the command kubectl apply -f deployment.yaml. Monitoring probes involves checking the liveness and 
  readiness statuses using kubectl get pod and kubectl describe pod <pod-name>. Customize probes according to specific application needs by adjusting parameters in the deployment.yaml file under the livenessProbe and readinessProbe sections. Troubleshooting tips include 
verifying the standalone functionality of the application, inspecting container logs, and reviewing pod descriptions for probe-related failures. </p>
