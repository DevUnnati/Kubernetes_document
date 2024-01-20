# Certificates and Certificate Signing Request:
 
<h3><b>Certificate Signing Requests:</b></h3>

CSR is a resource used to request that a certificate be signed by a denoted signer, after which a request may be approved or denied.


**Note:**
Approved Requests: Automatically deleted after 1 hour.
Denied Requests: Automatically deleted after 1 hour.
failed Requests: Automatically deleted after 1 hour.
Pending Requests: Automatically deleted after 24 hours.
All requests: Automatically deleted after the issued certificate has expired.


<h3><b>Certificate Signing Authorization:</b></h3>

**a) To allow creating a CSR and retrieving any CSR:**

   i) Verbs: create, get, list, watch
   
   ii) group: certificates.k8s.io
   
   iii) resource: certificatesigningrequests

**b) To allow approval a CSR:**

   i) verbs: (get, list, watch), groups: certificates.k8s.io, resource: certificatesigningrequests
   
   ii) verbs: update, group: certificate.k8s.io, resource: certificatesigningrequests/approval
   
   iii) verbs: approve, group: certificates.k8s.io, resource: signers, resourceName: <signerNameDomain>/<signerNamePath> or <signerNameDomain>/*
   
**c) To allow signing a CSR:**

   i) verbs: (get, list, watch), groups: certificates.k8s.io, resource: certificatesigningrequests
   
   ii) verbs: update, group: certificate.k8s.io, resource: certificatesigningrequests/status
   
   iii) verbs: approve, group: certificates.k8s.io, resource: signers, resourceName: <signerNameDomain>/<signerNamePath> or <signerNameDomain>/*


<h3><b>Kubernetes Signers:</b></h3>

**a) Built-in Signers:**

   Kubernetes provides built-in signers that each have a well-known signerName:
   
   i) kubernetes.io/kube-apiserver-client:--> honored by API server, Never auto-approved by kube-controller-manager.
   
   ii) kubernetes.io/kube-apiserver-client-kubelet:--> honored by API server. It may be Auto Approved by kube-controller-manager.
   
   iii) kubernetes.io/kubelet-serving:--> honored by kubelet, never auto-approved by kube-controller-manager.
   
   iv) kubernetes.io/legacy-unknown:--> honored by third-party distributions, no guarantee, never approved by kube-controller-manager.
   
**b) Custom Signers:**

   You can also introduce your custom signer, which should have a similar prefixed name but use your own domain name.
   
   For example, if you represent an open-source project that uses the domain open-fictional.example then you might use issuer.open-fictional.example/service-mesh as a signer name.
    A custom signer uses the Kubernetes API to issue a certificate.
	
<h3><b>Signing:</b></h3>

a) Control Plane Signer

b) API based Signers


<h3><b>Approval or Rejection:</b></h3>

a) ControlPlane Automated Approval

b) Approval or Rejection using kubectl:

   **Approve a CSR with Kubectl:**
   
     command: _kubectl certificate approve <CSR-name>_
   
  **Deny a CSR with Kubectl:**
  
     command: _kubectl certificate dent <CSR-Name>_
   
c) Approval or rejection using Kubernetes API
