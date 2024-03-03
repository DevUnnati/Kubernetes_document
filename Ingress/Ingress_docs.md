<h2> Ingress: </h2>

a) Ingress is an object that manages external access to the services in a cluster typically HTTP.

b) It may provide load balancing, SSL Termination, and name-based virtual hosting.

c) Ingress exposes HTTP and HTTPS routes from outside the cluster to services within the cluster.

d) Traffic Routing is controlled by rules defined on the ingress resources.

<h4> Terminology: </h4>

a)Node: A worker machine in Kubernetes, part of the cluster.

b) Cluster: A set of Nodes that run containerized applications managed by Kubernetes

c) Edge Router: A router that enforces the firewall policy for your cluster.

d) Cluster Network: A set of links, logical or physical, that facilitate communication within a cluster according to the Kubernetes.

<h4> Ingress Controller: </h4>

The Ingress Controller is responsible for fulfilling the Ingress, Usually with a load balancer.
The Ingress Controller also configures the edge router.

Additional Frontends to help handle the traffic.

Note: An Ingress does not expose arbitrary ports or protocols. Exposing services other than HTTP and HTTPS to the internet typically uses a service of the type

    service.type=NodePort or service.type=LoadBalancer.

<h4> Prerequisites: </h4>

a) You must have an ingress controller to satisfy an Ingress.

b) You may need to deploy an ingress controller such as--> ingress-nginx

c) A lot of ingress controllers are available you can choose others also.

<h4> Ingress Resource:</h4>

a) An Ingress needs apiVersion, Kind, metadata, and spec fields.

b) The name of an Ingress object must be a valid DNS subdomain name.

c) The Ingress Spec has all the information needed to configure a load balancer or proxy server.

d) If the ingressclassname is omitted, a default Ingress class should be.

c) There are some ingress controllers available that can be configured without class.

ex-The Ingress-NGINX controller can be configured with a flag --watch-ingress-without-class.

<h4> Ingress Rules:</h4>

a) An Optional Host:  In this, no host is specified, so the rule applies to all inbound HTTP traffic through the IP address specified.

b) A List of Paths:

c) A backend is a combination of service and port names as described in the service doc or CRD.
   HTTP requests to the address that matches the host and the path of the rule are sent to the listed backend.
   
<h4> Default backend:</h4>

a) An ingress with no rules sends all traffic to a single default backend and 
   .spec.defaultBackend is the backend that should handle requests in that case.
   
b) The default backend is a configuration option of the Ingress controller and is not specified in your ingress resources.

c) If none of the hosts or paths match the HTTP request in the Ingress objects,
    the traffic is routed to your default backend.   
   
<h4>Path Types:</h4>

In ingress three types of supported paths are available.
Implementation Specific: Implementation can treat this as a separate path type or treat it identically to Prefix or Exact path types.

a) Exact: Matches the URL path exactly and with case sensitivity.

b) Prefix: Matches based on a URL path prefix split by /.

Multiple Matches:

In some cases, Multiple paths within an ingress will match a request. In those cases, precedence will be given first to the longest matching path.

If two paths are still equally matched, precedence will be given to paths with an exact path type over prefix path type.

HostName WildCards:

a) Host can be precise Matches--> foo.bar.com or Host can be wildcard matches--> *.foo.com

b) Precise matches require that the HTTP host header matches the host field.

c) Wildcard matches require the HTTP host header to be equal to the suffix of the wildcard rule.


<h4>Ingress Class:</h4>

Ingress can be implemented by different controllers, often with different configurations.

Ingress Class scope is two types cluster-wide and just for namespace.

.spec.parameters.scope--> Cluster

.spec.parameters.scope--> NameSpace

Deprecated Annotation:

Ingress Classes were specified with a kubernetes.io/ingress.class annotation on the ingress.

<h4> Default Ingress Class: </h4>

a) You can mark a particular class as default for your cluster by setting

ingressclass.kubernetes.io/is-default-class = true

<h4>Type of Ingress class:</h4>

<h6>a) Ingress Backend by a service:</h6>

This type of Ingress allows you to expose a single service.

---
    apiVersion: networking.k8s.io/v1 
    kind: Ingress
    metadata:
     name: test-ingress
    spec:
      defaultBackend:
      service:
        name: test
	    port:
          number: 80
	
Output:

NAME           CLASS         HOSTS   ADDRESS         PORTS   AGE
test-ingress   external-lb   *       203.0.113.123   80      59s

<h6>b) Simple fanout:</h6>

A fanout configuration routes traffic from a single IP address to more than one service, based on the HTTP URL being requested.


client---> Ingress-managed loadbalancer--> Ingress,172.90.12.42 ---> /foo --> port:4200--> connected by two pod.
                                                       |
													   |
													   |----> /bar --> prt:8080 --> connected by wo pod
											
---
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: simple-fanout-example
    spec:
      rules:
      - host: foo.bar.com
        http:
         paths:
         - path: /foo
           pathType: Prefix
           backend:
             service:
               name: service1
               port:
                 number: 4200
         - path: /bar
           pathType: Prefix
           backend:
             service:
               name: service2
               port:
                 number: 8080
        
Note: Depending on the Ingress controller you are using, you may need to create a default-http-backend Service.


<h6>c) Name-Based Virtual Hosting:</h6>

Name Based Virtual Hosts support routing HTTP Traffic to multiple hostname at the same IP address.

client----> Ingress-managed loadbalancer ----> Ingress(172.91.123.132)-->Host: foo.bar.com --> Port:80--> Connected two Pod
                                                      |
                                                      |---> Host: bar.foo.com --->	Port:80 --> Connected two Pod
						      
---
     apiVersion: networking.k8s.io/v1
     kind: Ingress
     metadata:
       name: name-virtual-host-ingress
     spec:
      rules:
       - host: foo.bar.com
         http:
           paths:
            - pathType: Prefix
              path: "/"
              backend:
                service:
                  name: service1
                  port:
                    number: 80
       - host: bar.foo.com
         http:
         paths:
         - pathType: Prefix
           path: "/"
           backend:
             service:
               name: service2
               port:
                 number: 80
			 
			 
<h6> d) TLS: </h6>

You can secure an Ingress by specifying a Secret that contains a TLS private Key and Certificate.

The Ingress Resource only supports a single TLS port, 443.

The TLS secret must contain a key named tls.crt and tls.key that contains the certificate and private key to use for TLS.

The TLS secret you create comes from a certificate that contains a Common Name, also known as FQDN.

---
    apiVersion: networking.k8s.io/v1
     kind: Ingress
     metadata:
       name: tls-example-ingress
     spec:
      tls:
       - hosts:
          - https-example.foo.com
         secretName: testsecret-tls
      rules:
       - host: https-example.foo.com
      http:
        paths:
        - path: /
          pathType: Prefix
          backend:
           service:
             name: service1
             port:
               number: 80
			  
<h6> e) Load balancing: </h6>

An Ingress Controller is bootstrapped with some load balancing policy setting that it applies to all ingress, such as the load balancing algorithm, backend weight scheme, and others.


<h4> Updating an ingress: </h4>

To update an existing ingress to add a new host, you can update it by editing the resource.
		  
		  
<h4> Operations in Ingress:</h4>

a) get: read the specified ingress

b) list: list or watch objects of kind ingress.

c) create: create an ingress.

d) update: replace the specified ingress.

e) patch: partially update the specified ingress.

f) delete: delete an ingress.

g) deletecollection: delete a collection of ingress

			  
<h4> Ingress Controllers:</h4>

Ingress Controller is not started automatically with a cluster.
