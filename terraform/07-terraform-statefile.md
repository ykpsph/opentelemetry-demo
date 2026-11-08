what is state file and state file management ?
- state file 

main.tf'i yazdık çalıştırdık, AWS'te bir S3 bucket oluşturdu diyelim, state file bir nevi Terraform'un hafızası. 
main.tf tekrar çalıştırılırsa terraform statefile'a bakıp daha önceden zaten bu S3 bucket'ı oluşturduğunu hatırlayacak.

main.tf update edilirse ne olur peki ? terraform developer'ın yaptığı değişikliği uygular, önceden çalıştırdıklarını uygulamaya gerek duymaz ve statefile tekrar güncellenir ve son değişiklikleri de kaydeder.

Terraform keeps a record of it's execution :::: statefile