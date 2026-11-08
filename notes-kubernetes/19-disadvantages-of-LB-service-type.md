## Disadvantages of Load Balancer Service Type
```
LB -----> API -----> CCM -----> AWS -----> LB
|                                           |    
<--------------------------------------------
```

1. we created a service type of load balancer.
2. this instruction was taken by API server.
3. it passed this information to the Cloud Control Manager (CCM).
4. CCM got in contact with AWS (because our cloud provider is AWS).
5. AWS generated a load balancer.
6. This load balancer's IP is attached to the service type of load balancer.
7. and anybody from the external world become able to access to this service.


### Downsides of this approach
- configuration of the load balancer is declarative 
- not cost effective
- only ALB, can't change to other LBs.
- CCM depended (on AWS it's fine) 
- only advantage of using a service type of load balancer is easy to configure.

> Good approach is to go with **ingress** resource
- it is declarative (yaml) which means we can modify properties related to the load balancer
- cost effective
- ingress controller is not depended on CCM