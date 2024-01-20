# How to issue a certificate for a user

**Step 1:** Create Private Key

      openssl genrsa -out myuser.key 2048

      openssl req -new -key myuser.key -out myuser.csr -subj "/CN=myuser"


**Step 2:** Convert generated CSR in encrypted form:

     cat myuser.csr | base64 | tr -d "\n"

**Step 3:** Create a Certificate signing request:

     vi user.yaml

    apiVersion: certificates.k8s.io/v1
    kind: CertificateSigningRequest
    metadata:
       name: myuser
    spec:
      request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dkVzV1WVhScE1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQXdmUHUxZ09XVG1SdXlTVjRVQkwxVk4yL3V3cHMwMUxVMlNtR0YzclJSYWxLCmFITTNMa1JidzVibGxNMHFpeU0yUGlLSjRzUVFVT2IyaTJ4WmJxQVNxZUcxV29hQTFGa3JFeHNSZlZpenUxazMKc0ovOFhMQjFJbEYyYmFjdjRGa1JvdU82K2xkT2lsOTFsTmsvZndTUTFrb2VXa3VadTVyV2dpWm9XL1NmTWU3UQpGUzRNRlRENmsrcjR3aGVhMWxOU1pYeVlnTUdna1pZcC9ZK1ZEeEo5M2djb3VwbUdqUC95N0lIeHBNRlZjT3gwCkl0bFlkdUd3dlJNU0JwdVdDbC84UVozY2Y0d3hWUlNVREpoWTFIVjlnQzdodmpsVjZSLzBqY3A1TVljWWlleFYKVExVb1gydG0xb1JYc0lqR1B3V3krMEY2cnZnWVpQTDNyazF4bTJ6M25RSURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBRmFLbFRBYkh4YjJiZXg4dHNNY0c1V3pCOHM5eVpkbjZ0MHNFdUtXWXZ4OXZjcnFoVW1LCmRsUmtTN0hMN3Jua3V4TWwzZ0hmZE9ad2ltT1JLRGRsbERlcEczTnFiQm9mQkFmdFBOaWJYYlRGOFdrcCtBMzEKVVJhQSt3NEtwTXJPMHF2RVJQRmRpam1HNUdNUlBvdUoxNzhjajE2K1ZCZ2dvVU5nTkZTNkFPZ1BhNzFOM08zOApwWHNZR2tFSHIxMngvUTZKL2F4anNHQUIrNlZaajgxdFBNcnI0UnFlN29zUW5YeVFEMVB6R2RraW80SE5hNXRnCjJEbDVWbytFZXFWbkNsRTFFdjNFVktPUVVoTzU0K1pRbkZlZkpEQ3F5eVpnWXlIQ0tuQ0ovanl5YXpDR29BbGcKbWtWNWQvNkJiNzk5RzJKemFtNm4wZ2pSNDlMS3hYRFdqVHc9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
      signerName: kubernetes.io/kube-apiserver-client
      expirationSeconds: 86400  # one day
      usages:
      - client auth

    kubectl apply -f user.yaml

**Step 4:** Get the list of CSR or Verify CSR Created Successfully.

    kubectl get csr

**Step 5:** Approve the CSR

    kubectl certificate approve <csr-name>

**Step 6:** Get the certificate

    kubectl get csr/<csr-name> -o yaml

**Step 7:** Create Role

     kubectl create role developer --verb=create --verb=get --verb=list --verb=update --verb=delete --resource=nodes

**Step 8:** Create Role Binding ( For permitting accessing resources from Kubernetes cluster)

    kubectl create rolebinding developer-binding-myuser --role=developer --user=unnati

**Step 9:** Add to kube config

This step is to add a user in kubeconfig file.

    kubectl config set-credentials myuser --client-key=myuser.key --client-certificate=myuser.crt --embed-certs=true


**Step 10:** Then add the context

       kubectl config set-context unnati --cluster=kubernetes --user=unnati

**Step 11:** To test it, change the context to the newly created context

      kubectl config use-context myuser

**Step 12:** To get the context list

       kubectl config get-contexts
