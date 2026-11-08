# Deploying the Project on EKS cluster

1. verify if you are connected to EKS cluster.
```yaml
>_ kubectl config corrent-context
```

2. make sure none of microservices are deployed
```yaml
>_ kubectl get all
``` 

3. Create service account
   
![ls -ltr](/notes/images/image-15.png)

![name](/notes/images/image-16.png)

```yaml
>_ kubectl apply -f serviceaccount.yaml
```   
![serviceaccount](/notes/images/image-17.png)

4. Deploy
```yaml
>_ kubectl apply -f complete-deploy.yaml
```

5. to see the all services, run the following :
```yaml
>_ kubectl get svc
```

6. they all must be running, to check the status
```yaml
>_ kubectl get pods
```
7. Access the website, but.... EKS is in the VPC
> the IP address of the frontendproxy service is a private IP address.
![alt text](/notes/images/image-18.png)

8. we have to change it's service type from *ClusterIP* to *LoadBalancer* and CCM will create a load balancer. Copy the DNS name and access from your browser, if you can access the website it means that you have the project deployed on kubernetes (eks).
```yaml
>_ kubectl edit svc opentelemetry-demo-frontendproxy
```




