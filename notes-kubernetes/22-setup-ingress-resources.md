## Creating Ingress Resource

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: frontend-proxy
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
spec:
  ingressClassName: alb
  rules:
    - host: example.com
      http:
        paths:
          - path: "/"
            pathType: Prefix
            backend:
              service:
                name: opentelemetry-demo-frontendproxy
                port:
                  number: 8080

```

- and apply. 
```yaml
>_ kubectl apply -f ingress.yaml  
 ```
- load balancer will be created, check the address
```yaml
>_ kubectl get ing
```
- in the ingress.yaml we declared that the host is example.com, we can't access the site using the DNS name or IP address. We will add the IP address into our **local** DNS records. And ingress will allow us to access using the host.
```yaml
>_ sudo vim /etc/hosts

<IP-Address> example.com
```