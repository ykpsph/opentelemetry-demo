# Kubernetes Resources
 ### Deployment
 <br>
- if you want to deploy this application on local environment, containers are fine, but containers are **ephemeral** in nature. That means they are very short lived. 
- And anytime if the container goes down and if it comes up, the IP address of containers is going to change.

    - That is one of the reasons why Kubernetes is used in your local environment, higher environment as well as the staging environment.

There are two primary reasons to choosing Kubernetes : 
1. Scaling and healing features of Kubernetes
2. Service discovery of Kubernetes

![kubernetes](/notes/images/image-12.png)

note : scaling and healing are handled by deployment resource of kubernetes
note : service discovery is handled by service resource of kubernetes

### What is Scaling and Healing ?
- Let's say you deployed a container, by default if you don't have any restart policy, if the container goes down and if you don't have restart policy, the container does not come up. That is, there is no auto healing for containers by default.

  - And similarly, let's say you have deployed the container and you know, during the peak peak time, since we are talking about e-commerce applications. During the peak sale time you would need high availability for your containers.
  - That means just one copy of your container will not be enough. You would need multiple copies of your container and you need a load balancer balancing the node.


So here we are talking about two things.
- scaling
- healing
> These particular things are provided by a resource of Kubernetes which is called as **deployment**.

- Deployment is a very popular resource of Kubernetes in fact, most of the time, you would deploy your microservices in Kubernetes using the deployment resource.

#### So what exactly this deployment resource does to implement these things?
1. In Kubernetes there is the lowest level of deployment which is called as **Pod**. You can consider it as somewhat equivalent to a container in a world of Docker.
However, a pod can be a single container or multiple containers as well.
But just like how container holds your microservice, a pod is a resource in Kubernetes that actually holds your microservice.

2. What Kubernetes does is, because a container goes down in the typical container environment because of multiple reasons, it asks users to use a deployment resource called **deployment**.
And this resource called deployment will handle your pod.
So when you want to deploy a microservice, if you create that microservice as **deployment**, it will create an intermediate resource called **replica set**.
And this **replica set** will spin up the containers or the pods in Kubernetes world. 

3. in the deployment YAML file if you say replica count as 3, then the intermediate resource called replica set, it is going to make sure that all the time the pod count is 3.
That means for some reason, if pod goes down, that is the microservice in the pod goes down and because of that, if the pod goes down, replica set will immediately create a new pod.
So it will always make sure that the count is as mentioned in the deployment.

4. `So what problem it is solving?` - It is solving the problem of **auto healing**. So in deployment, if you mention the replica count as four immediately it will create a replica set.
And this replica set will ensure that always four copies of your container are running within the pods.

5. So the replica set is making sure there are 4 replicas. For some reason, a pod has gone down, immediately it will create a new pod and it will connect that pod.
A auto healing is automatically implemented. So a **replica set** is a controller in Kubernetes which is taking care of these things.
Peki, who creates the replica set? It is a **deployment resource**. 

> So that's why overall a deployment resource is the one that takes care of healing and scaling in Kubernetes.

### Service
- Service solves a very critical problem in the world of containers that is service discovery. (one of the most important reasons why container orchestration solutions like Kubernetes have become very popular.)

- Let's say there are two containers. FE container and BE container.
And assume in your staging environment you deployed your FE and BE. and the FE container as you deployed it using Docker.
- Let's assume FE got the IP address 10.10.2.3 and BE got the IP address 10.10.4.6
- You connected your FE with your BE and everything was going fine but after 24 hours for some reason your BE went down. 
- And you mentioned the restart policy as *true* in containers or one of DevOps engineers has realized that backend has gone down and they instantly restarted it.

You will assume that your FE will connect to the BE again, but the problem with service discovery of containers is that next time when this comes up, the IP address might change to 10.10.5.4

`Now what problem does it create?`
- Although the BE is up because the FE environment variable tells that this is the BE IP address, it will continuously keep looking for the BE at this IP address, the user who
tries to access to the FE.
- Only solution is that you will have to go to the FE container, update the environment variable and also restart the front end container. This will be fine until and unless the BE is restarted one more time.
- So this problem is called as **service discovery problem**. Because this is about discovery of microservices or monoliths. 
- The discovery will fail if you are just using containers.
  

`Now how Kubernetes address this problem is using services?`

In Kubernetes, you deploy your microservices using a resource called Deployment Resource.
And what it does is it will create an intermediate resource called replica set.
Let's assume for FE we created a deployment that deployment created replica set.
And the replica set controller and assume that it created one copy of FE.

- Similarly for BE also we will create a deployment which will in turn create a replica set and which creates a copy of BE service. (to put it simple, we do it with a single deployment file)

`Now, how does discovery problem is solved in this case?`
- This would have been a problem if Kubernetes just had the deployment resources, because deployment
is only solving the problem of scaling and healing.
- Kubernetes goes a level beyond and says we have something called as a service resource where now the FE pod will not directly communicate to the BE. Instead of FE talking to the BE  it will talk to a load balancer/service and that will talk to the BE.
- The magic of the `service` is that it does not operate with the IP addresses. What a service operates with is a concept called as labels and selectors.
- So a service looks at a particular label on the BE and this label has to be unique across your different services of organization.
- Yani, when BE goes down it might come up with a new IP address, but still thelabel will be the same.
> 1. Yaniiii, the problem of service discovery in Kubernetes is solved by the BEs. Because the BE pod and the FE pod which are created by the deployment, they don't communicate with each other directly.
> 2. If FE has to talk to BE in Kubernetes, the ideal behavior is that the FE developer
will have an environment variable for BE, but that environment variable will be the name of the
service.

So this is how the problem of service discovery is solved in Kubernetes.
So both deployment and resource services are two important concepts of Kubernetes.
Their deployment is solving the problem of scaling and healing.
And service is solving the problem of service discovery.


