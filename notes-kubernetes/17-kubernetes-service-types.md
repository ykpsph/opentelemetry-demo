# Kubernetes Service Types 
1. ClusterIP  --------> service to service (yani pod to pod) communication within the cluster   (internal)
2. NodePort -----> external access to a service through a port on the kubernetes node (in the vpc)  (external via node)
3. Load Balancer -----> external access to a service through an external Load Balancer          (external via load balancer)