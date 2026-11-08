## how to connect to 1 or many Kubernetes cluster from command line ?
- kubectl depends on a file called kubeconfig
- you can provide list of clusters in your organization in the kubeconfig or list of clusters you want to connect to

- in the kubeconfig file, there is something called *context* which tells kubectl which cluster it is connected to at this point of time.
- using kubectl you can change the context and connect to another cluster.

- right now kubectl says the list of clusters information that I have is null. That means I don't have any information of the context. If you give me the Kubernetes clusters then I can connect to it.

![kubectl config view](/notes/images/image4.png)

- the command below tells from all the clusters we have which one we currently have connected.
![kubectl config current-context](/notes/images/image5.png)

- if you want to switch between clusters you run this command : >_ kubectl config use-context <cluster-context-name>
![switch context](/notes/images/image-10.png)

## Configure EKS
- we have to use aws configure command to configure our EKS 
>_ cat ~/.aws/credentials 
- you need to pass credentials
context is nothing but kubernetes related information

![update-kubeconfig](/notes/images/image-6.png)

if you run kubectl config view : you can see everyhing about your EKS cluster
![kubectl config view](/notes/images/image-7.png)

- this is what you'll see in your local

![alt text](/notes/images/image-8.png)

- if you're using EKS, then you'll see

![alt text](/notes/images/image-9.png)