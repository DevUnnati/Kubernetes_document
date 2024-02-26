apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: frontend
spec:
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          role: backend

kubectl run nginx7 --rm -it  --image=nginx -n database -- curl http://nginx1.frontend:8080
