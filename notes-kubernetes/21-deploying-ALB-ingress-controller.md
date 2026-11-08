## Deploying ALB Ingress Controller
- Before installing the AWS Load Balancer Controller on an EKS cluster:
1. make sure you are connected to the correct EKS cluster
```yaml
>_ kubectl config current-context
```
2. make sure eksctl is installed
```yaml
>_ eksctl version
```
3. export the cluster name
```yaml
>_ export cluster_name=my-eks-cluster
```
4. fetch the OIDC id
```yaml
>_ oidc_id=$(aws eks describe-cluster --name $cluster_name --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5)
>_ echo $oidc_id
```
5. associate IAM OIDC provider with your cluster
```yaml
>_ eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve
```
### NEXT STEPS
1. create an IAM policy which will have permissions for ELB
2. create an IAM role and attach it to the service account of ALB controller

- to download IAM policy
 ```yaml
>_ curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
 ```
- to create the IAM policy
```yaml
>_ aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
```
- to create IAM role  (remember to provide your aws account id and cluster name)
```yaml
eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```
- add helm repo and update it
```yaml
>_ helm repo add eks https://aws.github.io/eks-charts
>_ helm repo update eks
```
- install the ALB controller (again, don't miss to fill the required information)
```yaml
>_ helm install aws-load-balancer-controller eks/aws-load-balancer-controller \            
  -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<region> \
  --set vpcId=<your-vpc-id>
```
- verify that the deployments are running
```yaml
>_ kubectl get deployment -n kube-system aws-load-balancer-controller
```
- verify that the ALB pods are running
```yaml
>_ kubectl get pods -n kube-system
```
- last : check if there are any errors in the logs, if there are no errors, that means ALB controller installation is successful
```yaml
>_ kubectl logs aws-load-balancer-controller-xyz -n kube-system
```








## Why do we need IAM OIDC ?
- The AWS Load Balancer Controller runs as a Pod inside EKS, but it needs to create AWS resources such as an ALB outside the Kubernetes cluster.
- A Pod normally cannot automatically create AWS resources.
- We need to give the controller AWS IAM permissions.
- The basic flow is : 
```
AWS Load Balancer Controller Pod
            ↓
      Service Account
            ↓
       IAM OIDC Provider
            ↓
          IAM Role
            ↓
     IAM Policy / Permissions
            ↓
       AWS ALB resources
```

# What is OIDC doing ?
- IAM OIDC connects a Kubernetes Service Account to an AWS IAM Role.
- The IAM Role contains the permissions required by the AWS Load Balancer Controller.
```
Service Account
      ↓
   OIDC
      ↓
   IAM Role
      ↓
Permissions to create/manage ALB
```
- Therefore, the controller Pod can perform AWS operations without giving the Pod AWS access keys.

>>>>> Interview question : How can a Pod inside EKS create/manage an AWS resource outside the cluster?
- The Pod uses a Kubernetes Service Account that is associated with an AWS IAM Role through an IAM OIDC provider. The IAM Role has the required permissions to create or manage AWS resources.