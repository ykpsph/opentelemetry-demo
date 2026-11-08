 ## Ingress and Ingress Controller
 **ingress** refers to *incoming traffic* in the networking world.

 - In Kubernetes, **Ingress** is a resource (*kind: Ingress*) used to define routing rules for incoming traffic.just like how Kubernetes has resource of kind deployment, service config, secret, it also has a resource of *ingress*, 
 - where it can help you with defining the routing rules for your incoming traffic. Meaning, using Kubernetes kind: Ingress you can actually define rules on your incoming traffic to the cluster.
> important : creating an Ingress resource by itself doesn't create a load balancer or make the application accessible.
- An Ingress Controller reads the Ingress resources and implements those rules.


- `what I did?` I have created the EKS cluster using Terraform. And within the EKS cluster that is in a VPC, I deployed a FE service 
- FE service directly comes up with *ClusterIP* service type that it is only accessible within the cluster. 
- to service to be accessible from external world I exposed the service type to load balancer. 
- API server instructed the cloud control manager (CCM), which created a load balancer within the VPC, and this load balancer can talk to the FE using the private subnet. 
- at the same time, this load balancer is also connected to the public subnet so using load balancer DNS name, it became accessable from the external world.


### Service Type LoadBalancer vs Ingress
1. without ingress : Internet --> Load Balancer --> Frontend Service --> Frontend Pods
- if the service is type: LoadBalancer, the Kubernetes Cloud Control Manager (CCM) can provision a cloud load balancer. And the load balancer sends traffic to the Kubernetes Service.
2. with ingress : Internet --> Load Balancer --> Ingress Controller --> Service --> Pods
- the difference is that the Ingress Controller uses the rules defined in the Ingress resource to decide where the incoming traffic should go.
#### How Ingress Works
1. Create Ingress Resource
2. Ingress Controll reads it
3. Controller configures the traffic routing
4. Incoming request reaches the appropriate Service
5. Service forwards request to Pods

#### Ingress YAML Example
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name: frontend-ingress

spec:
  rules:
    - host: amazon.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend
                port:
                  number: 8080
```
- host: amazon.com -----> host-based routing
- path: / -----> path-based routing
- service:
    name: frontend -----> the request is ultimately sent to the frontend service
- port: 8080 -----> the service port receiving the request

#### Why do we need Ingress ?
- A normal Kubernetes Service has limited traffic-routing configuration.
```
Service
 ├── ClusterIP
 ├── NodePort
 └── LoadBalancer
```
- Using LoadBalancer for every externally accessible service, will result in having multiple load balancers : 
```
             Internet
                │
       ┌────────┼────────┐
       ↓        ↓        ↓
     LB #1    LB #2    LB #3
       ↓        ↓        ↓
    Service  Service  Service
```
- with Ingress, we can have a common entry point and define routing rules : 
```
                    Internet
                       │
                       ↓
                Load Balancer
                       │
                       ↓
               Ingress Controller
                  /     |      \
                 /      |       \
                ↓       ↓        ↓
           frontend   product   checkout
            Service   Service    Service
```

### Ingress Resource 
1. we create an Ingress YAML that says:
"When someone accesses example.com, send the request to my frontend service."
So the `Ingress Resource = rules`.

### Ingress Controller
The Ingress Controller reads those rules and actually implements them. You have to install/deploy an Ingress Controller yourself. Which one you use depends on the load balancer / technology you want:
```
NGINX          → NGINX Ingress Controller
AWS ALB        → AWS Load Balancer Controller
```
#### Flow in my demo project 
- Ingress Resource defines the rules. Ingress Controller reads those rules and creates/configures the necessary load-balancing infrastructure.
- Ingress Controller must be deployed for an Ingress Resource to have an effect. The controller watches Ingress Resources, reads their routing rules, and configures/provisions the required load-balancing infrastructure. In our EKS project, we use the AWS Load Balancer Controller to create and configure an AWS ALB.
```
                    Internet
                       ↓
                      ALB
                       ↓
          AWS Load Balancer Controller
                       ↑
                       │ reads
                       │
                Ingress Resource
                       ↓
              Frontend Service
                       ↓
                Frontend Pods
```
- Steps to follow : 
```
1. Deploy AWS Load Balancer Controller
              ↓
2. Create Ingress Resource for frontend
              ↓
3. Controller sees the Ingress
              ↓
4. Controller creates/configures an AWS ALB
              ↓
5. ALB sends traffic to frontend
```