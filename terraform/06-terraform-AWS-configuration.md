

Terraform will make API calls to AWS for that we have to authenticate terraform with AWS. 
To do that terraform will need our IAM credentials.

1. create security credentials for the IAM user 
2. install aws cli on our local machine
3.  run AWS cli commands to configure, pass the credentials

security credentials >>> access keyss (create) >>> select use case : command line interface >>> next >>> create access key 

```yaml
aws configure
```