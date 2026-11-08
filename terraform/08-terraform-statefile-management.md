User1 main.tf'i yazdı diyelim, bu HCL script'e EC2 instance ve S3 bucket oluşturmasını söyledi

User2 main.tf'te değişiklik yapıyor : ve terraform apply'ı çalıştırdığında fark ediyor ki tekrar EC2 instance oluşturulmaya tekrar S3 bucket oluşturulmaya çalışıyor. Peki neden ? çünkü statefile User1'in kendi localinde. Yani User2 main.tf'i kendi localine çekip çalıştırdığında statefile kendi localine gelmiş olmadığı için kendi localinde statefile boş.....


Peki User1, main.tf'i push'ladığı gibi statefile'ı da push'layamıyor muydu ? hayır çünkü statefile'da sensitive information'lar vardır. Load balancer information, NAT gateway information, IP addresses of subnet... 


statefile is local to machine

statefile management için 2 concept vardır : 
1. remote backend configuration
2. state locking


