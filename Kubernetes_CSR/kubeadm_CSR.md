----Administration with Kubeadm

a) Certificate Management with Kubeadm

i) Client Certificate generated by kubeadm expire in 1 year but you can renewal the certificate

PKI certificates and requirements:

Kubernetes requires PKI certificates for authentication over TLS.

If install kubeadm --> certificate for cluster is automatically generated.

You can also generate your certificate --> to keep your private keys more secure without sharing with API server.

How certificate are used by your cluster:

a) Client Certificates:

--> for the kubelete to authenticate to the API server.

---> for the administrator of the cluster to authenticate to the API server.

---> for the API server to talk to the kubelets

---> for the API server to talk to etcd

---> for the Controller Manager to talk to the API server.

---> for the Scheduler to talk to the API server

---> Client and server Certificate for the front-proxy.

Note: front-proxy certificates required only if you run kube-proxy.



b) Server Certificate:
Kubelete server certificate for the API server to talk to the Kubelets
Server Certificate for thr API server endpoint

ETCD also authenticate mutual TLS to authenticate clients and peers.

Certificates location:
a) by installing kubernetes with kubeadm --> certificates stored in ---> /etc/kubernetes/pki
b) User Account certificate --> /etc/kubernetes

Note: if certificate already present thier kubeadm will not be overwrite them
kubeadm cannot manage certificate signed by external-CA.
Configure Certificate manually:
you can create them using a single root CA


Check Certification expiers
kubeadm certs check-expiration


Automatic Certificate renewal:
a) Kubeadm renews all the certificares during control plane upgrade.
b) When you upgrade your control plane, it automatically renew all certificates.
c) if you don't want to renew your certificate during cluster upgrade, you can use this tag --certificate-renewal=false by using kubeadm upgrade apply 
before kubeadm version1.17 there is one bug ---N by default this option is --certificate-renewal=false.

Manual Certificate Renewal:
You can renew your certificate any time by using this command --> kubeadm certs renew
This is ok for dynamic pods.
for static pods are managed by local kubelet and not by the API server, to restart a static Pod you can temporarily 

Renew Certificate with Kubernetes API:
how to execute manual certificate renewal using the Kubernetes certificates API.
a) set up a signer
b) Create Certificate Signing request

Renew Certificate with External CA