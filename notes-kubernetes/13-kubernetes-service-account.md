
- you should definitely assign your pod to a service account.
- There are two things : user account and service account

### What is a User Acount ? 


- user account is basically for users like us or for humans.
- If I want to connect to a Kubernetes cluster I need a user account, and that user account will have a kube config file.
- Using kubectl you connect to that Kubernetes cluster.
- So if it is a DevOps engineer, developer engineer, if they want access to the Kubernetes cluster or
the Kubernetes user interface, then you should create a user account for them.

### What is a Service Account ? 

- What is service in general mean?
    - if you want to deploy a pod to Kubernetes and this pod is basically a microservice or it is an application.
    - So for this microservice or application to run on Kubernetes, just like how a user has user account, this service should have a service account by user requires permission to do something on Kubernetes.
    - Similarly, service also requires permission to do something on Kubernetes and that permission comes through the service account.
    
    ![service-accounts](/notes/images/image-11.png)


    - So whenever we usually run demo projects on Kubernetes for practice, we usually don't assign a service account to the pods that we are deploying.
    - However, by default, Kubernetes has a service account called **default** in every namespace.
    - So if you don't assign service account to your pod, Kubernetes is assigning that default service account only.
    - Because of that, your Kubernetes pod is able to run on the Kubernetes cluster.
    - Imagine if Kubernetes did not create this default service account for you. Then your pod will fail by saying it is not assigned with a service account. `But why do you need a service account?` Just like users have user account, a pod has a microservice or a application, and that service requires a service account as a user.
    - If you want to access Configmaps, you need to first login to the Kubernetes cluster with your user account. Similarly, if a service in Kubernetes, if it wants to access the Kubernetes API server, then it needs a service account with permissions, just like how user account requires permissions.
    - Service account also needs permissions. If you don't give any permissions, it will just have the default permissions, so default permission will have the capability to run your pod.
    - If you want to go 'beyond' that, for example access Kubernetes APIs using this microservice or do something related to webhook configuration, or write an admission plugin, in that case, your service account needs permissions. That is elevated permissions.
    - For that, you will create a role or cluster role in Kubernetes. In this role or cluster role, you will define the permissions and you will bind this role to the service account using role binding or cluster role binding.

That's it.
