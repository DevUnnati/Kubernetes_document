# DNS For Services and Pods

Kubernetes creates DNS records for Services and Pods. You can contact services with consistent DNS name instead of IP addresses.

Kubelete configures Pods DNS so that running containers can lookup services by name rather than IP.

<h3><b>Namespaces of Services:</b></h3>

DNS Queries that don't specify a namespace are limited to the pod's namespace.

Access Services in other namespace by specifying it in the DNS Query.


ex-
A pod name is in the test namespace and a data service is in the prod namespace.

To access that service you have to send query <servicename>.<namespace>

DNS queries may be expanded using the pod's /etc/resolv.conf.

Kubelete configures the files for each pod.

    resolv.conf

    nameserver 10.32.0.10

    search <namespace>.svc.cluster
    
    options ndots:5

<h3><b>DNS Record
What object gets DNS record?**</b></h3>

a) Pods

b) Services


**Services:**

_**a) A/AAAA records:**_

  i) Normal (not headless) Services are assigned DNS A and AAAA records, depending on the IP family or families of the service.
  
         Form: my-svc.my-namespace.svc.cluster-domain.example ( resolves Cluster IP of the services)
        
  ii) Headless Services (without a cluster IP) Services are also assigned DNS A and AAAA records.
  
         Form: my-svc.my-namespace.svc.cluster-domain.example
	  
_**b) SRV Records:**_

   i) SRV records are created for named ports that are part of normal or headless services.
   
        For each named port, the SRV record has the form: _port-name._port-protocol.my-svc.my-namespace.svc.cluster-domain.example
      
        For regular service, it resolves the port number and domain name: my-svc.my-namespace.svc.cluster-domain.example
      
        For headless Service, this resolves to multiple answers: hostname.my-svc.my-namespace.svc.cluster-domain.example

**Pods:**

_**a) A/AAAA records:**_

    i) Kube-DNS versions, before the implementation of the DNS specification have the following DNS resolution:
    
           Form: pod-ipv4-address.my-namespace.pod.cluster-domain.example
           
    ii) Any Pod exposed by a service has the following DNS resolution available:
    
           Form: pod-ipv4-address.service-name.my-namespace.svc.cluster-domain.example

<h3><b> Pod's hostname and subdomains fields:</b></h3>

Normally, when a pod is created, its hostname is the pod's metadata. name value.

The pod spec has an optional hostname field, which can be used to specify a different hostname.

When specified it takes precedence over the pod's name to be the hostname of the pod

The pod spec also has an optional subdomain field which can be used to indicate that the pod is part of a sub-group of the namespace.

A Pod with spec.hostname--> foo

           spec.subdomain --> bar
           
		   namespace--> my-namespace
     
FQDN will be foo.bar.my-namespace.svc.cluster.local

		
		
<h3><b> DNS Policy:</b></h3>

a) ClusterFirst --> default DNS policy

b) ClusterFirstWithHostNet

<h3><b> Pod's DNS Config:</b></h3>

Pod's DNS Config allows users more control over the DNS settings for a Pod.

The DNS config field is optional and it can work with any dnspolicy settings.

However, when the DNSPolicy is set to None, the dsconfig field has to be specified.

a) nameserves: You must be specified at least one and at most 3.

b) searches: Kubernetes allows up to 32 search domains

c) options: An option list of objects where each object may have a name property (required) and a value property (optional).

_DNS serach domain list limits: 2048_
