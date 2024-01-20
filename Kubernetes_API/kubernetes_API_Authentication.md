# Kubernetes API Authentication:

<h3><b>Users in Kubernetes:</b></h3>

In every Kubernetes cluster two types of user exist:

a) Service Account managed by Kubernetes

b) Normal Users

<h3><b>Authentication Strategies:</b></h3>

Kubernetes uses 

a) client certificates

b) bearer tokens

c) Authenticating Proxy

As HTTP requests are made to the API server, Plugins attempt to associate following attributes with the request:

a) Username

b) UID

c) Groups

d) Extrafields

**You can enable multiple authentication methods at once.You should usually use atleast two methods.**

a) service account token foe service accounts.

b) atleast one other module for user authentication.

<h3><b>Authenticating Protocols:</b></h3>

LDAP, SAML, Kerberos, Alternate x509 schemes.

**a) Client Certificate:**

   i) X509 client Certificate:
  
   a) Client Certificate authentication is enabled by passing the--client-ca-file=SOMEFILE
	   
	   OpenSSL command line tool to generate a certificate signing request:
	   
	   _openssl req -new -key unnati.pem -out unnati-csr.pem -subj "/CN=unnati/O=app1/O=app2"_
	   
**b) Bearer Token:**

    i) Static Token File:
    
	   a) The API server reads broker tokens from a file when given the --token-auth-file=SOMEFILE.
	   b) The token list cannot be changed without restarting API server.
	   c) The token file is a CSV file with a minimum of 3 column:
	      i) Username
			  ii) UID
			  iii) Optional group name
    	d) If you have more than one group the column must be double quoted e.g.
            token,user,uid,"group1,group2,group3"
	
	  ii) Bearer token in request:
   
	    When using bearer token authentication from an http client, the API server expects an Authorization header with a value of Bearer <token>. The bearer token must be a character sequence that can be put in an HTTP header value using no more than the encoding and quoting facilities of HTTP. For example: if the bearer token is 31ada4fd-adec-460c-809a-9e56ceb75269 then it would appear in an HTTP header as shown below.

         Authorization: Bearer 31ada4fd-adec-460c-809a-9e56ceb75269
	
	  iii) Bootstrap Token:
   
	   a) Kubernetes includes a dynamically-managed Bearer token type called a Bootstrap Token.
    
		 b) These tokens are stored as Secrets in the kube-system namespace.
   
		 c) Controller Manager contains a TokenCleaner controller that deletes bootstrap token as they expire.
   
		 d) The Token are the form of [a-z0-9]{6}.[a-z0-9]{16}
   
		 e) The first component is TokenID and second is Token Secret.
   
		 f) For enable BootstrapAuthenticator --enable-bootstrap-toekn-auth-file
   
		 g) For enable Token Cleaner Controller --controllers=*,tokencleaner.
   
	  iv) Service Account Token:
   
	    A service account is an automatically enabled authenticator that uses signed bearer tokens to verify requests.
     
		  This plugin takes two optional flags:
    
		  a) --service-account-key-file
    
		  b) --service-account-lookup
    
		 Service accounts are usually created automatically by the API server and associated with pods running in the cluster through the ServiceAccount Admission Controller. Bearer tokens are mounted into pods at well-known locations, and allow in-cluster processes to talk to the API server. Accounts may be explicitly associated with pods using the serviceAccountName field of a PodSpec.
		
		For Manually created service Account.
  
		  command: kubectl create serviceaccount jenkins
  
		Create an associated token:
  
		  command: kubectl create token jenkins
  
		The created token is a signed JSON Web Token (JWT).The signed JWT can be used as a bearer token to authenticate as the given service account.
		
	v) OpenID Connect Tokens:
 
	   OpenID Connect is a flavour of OAuth2 supported by some OAuth2 providers, notably Microsoft Entra ID,Salesforce and Google.
	   
	vi) Webhook Token Authenticator:
 
	    Webhook Authenticator is a hook for verifying bearer toekns.

**c) Authentication Proxy:**

The API server can be configured to identify users from request header values, such as X-Remote-User. It is designed for use in combination with an authenticating proxy, which sets the request header value.

a) --requestheader-username-headers --> The first header containing a value is used as the username.

b) --requestheader-group-headers  ---> header name to check, in order, for the user's groups.

c) --requestheader-extra-headers-prefix

**Anonymous Requests:**

When enabled, requests that are not rejected by other configured authentication methods are treated as anonymous requests, and given a username of system:anonymous and a group of system:unauthenticated.


**User Impersonation:**

A user can act as another user through impersonation header.

Impersonation  requests first authenticate as the requesting user, then switched to the impersonated user info.

a) A user makes an API call with their credentials and impersonation headers.

b) API server authenticates the user.

c) API server ensures the authenticated users have impersonation privileges.

d) Request user info is replaced with impersonation values.

e) Request is evaluated, authorization acts on impersonated user info.
		
HTTP headers can be used to performing an impersonation request:

a) Impersonate-User

b) Impersonate-Group

c) Impersonate-Extra-( extra name)

   command: _kubectl drain node --as=superman --as-group-system:master_
