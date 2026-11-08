User1 main.tf'i yazarken remote backend'i configure ediyor 

statefile User1'in local'inde ya da git repository'de değilde S3 bucket'te saklanıyor buna remote backend deniyor 

bunu main.tf 'te de yapabilir backend.tf'te de yapabilir

kafan karışmasın diye örneği değiştiriyorum : main.tf'te EC2 instance ve RDS instance oluşturduğunu varsay, statefile management ile biz bu main.tf ya da backend.tf'te diyoruz ki bu istediğim kaynakları oluştur ve sonunda statefile'ı yeni bir S3 bucket içinde sakla. bu tam anlamıyla statefile managent 'ı remote backend ile sağlamak oluyor.


Ve bu sayede User2, main.tf'i ve backend.tf'i local'ine çekip main.tf'te değişiklikler yapıp çalıştırdığında yani terraform apply'ladığında main.tf backend.tf'e bakacak ve statefile'ın S3 bucket'ta olduğunu anlayacak ve benim hafızam orada deyip bakacak böylece User2'inin değişiklikleri gerçekleştirilecek.


S3 'de de bir bucket policy ile bu bucket'a kimlerin erişebileceğini kolayca belirleriz.