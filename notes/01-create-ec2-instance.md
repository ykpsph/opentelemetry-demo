## Create an EC2 Instance
- To be able to create resources on AWS, you user account have to have the right permissions. For now I attach my user the AdministratorAccess policy, which basically can perform any operation on AWS.

- AWS'te herhangi bir resource oluşturabilmek için account'un gerekli permission'lara sahip olması zorunlu. Şimdilik AdministratorAccess 'i direkt user'ımıza attach edebiliriz, sonrasında sadece gerekli permission'larla least privilidge sağlarız.

```
instance name : devops-demo
os : Ubuntu
instance type : t2.large
key pair : create new key pair call it devops-demo private key file format will be .pem
network settings : Auto-assign public IP must be enabled && Allow SSH traffic from should be selected and set to Anywhere
```

## After creating the EC2 instance 
- go to the directory where .pem was downloaded
```shell
ssh -i devops-demo.pem <user-name>@<public-IP-address>
```
> enter yes. If you get Permissions for 'devops-demo.pem' are too open, run the following command : 
```
chmod 400 devops-demo.pem
```
and run the ssh command again, for example : 
```
ssh -i devops-demo.pem ubuntu@3.108.185.150
```