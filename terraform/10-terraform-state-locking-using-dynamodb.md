Terraform kodunun git repo'da olduğu senaryoda User1 ve User2 aynı anda repository'i clone edebilme ihtimalleri var. Ve kendi requirement'larına göre  main.tf'te değişiklik yapabilir ve aynı anda terraform apply çalıştırabilirler ve bu sebeple aynı anda AWS'e API call gidecek. Biri bir şey istiyor diğeri başka bir şey istiyor EC2 ile alakalı peki ne olacak?

Bu senaryo locking mechanism ile avoid edilebilir

S3 is used for remote backend  ------> having statefile locally will create a lot of confusion and Terraform's memory will not be updated
DynamoDB is popularly used as state locking resource --------> Two devops engineers might try to run same Terraform configuration at the same time or during execution of another