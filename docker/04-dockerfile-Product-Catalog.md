- burada seçtiğimiz servisin önce Dockerfile'ını oluşturuyoruz ardından image oluşturuyoruz ve ardından container oluşturuyoruz : Dockerfile >>> image >>> container

1. Dockerfile'ı yazmaya başlıyoruz. Bu bir Go application olduğu için image golang:1.22-alpine image'ini seçiyoruz version 1.22 ve biz multistage dockerbuild yazdığımız için first stage'i builder olarak adlandırıyoruz

2. Work directory seçiyoruz, mandatory değil ama good practice : WORKDIR /usr/src/app it's just like a directory for the docker file or the docker commands execution

3. source code'u kopyalamamız gerekiyor 

4. download binaries : go.mod'tan binary'leri şu şekilde indiriyoruz ==> go mod download bu ilk step. 2. step ise go build -o <name> .         burada . source code anlamına geliyor


# Product Catalog Service Dockerfile
```docker
FROM golang:1.22-alpine AS builder

WORKDIR /usr/src/app

COPY . .

RUN go mod download
RUN go build -o product-catalog ./

FROM alpine AS release

WORKDIR /usr/src/app

COPY ./products ./products
COPY --from=builder /usr/src/app/product-catalog ./

ENV PRODUCT_CATALOG_PORT=8088
ENTRYPOINT ["./product-catalog"]
```

5. birinci stage'te binary'leri indirdik şimdi yeni bir stage yazacağız ve 1. stage'teki binary'yi buraya kopyalayacağız 1. stage'teki her şey remove olacak ve 2. stage bizim container kısmımız oluyor ve 2. kısımda özellikle very light image seçiyoruz en az binary'e sahip bir image

![alt text](/notes/images/image-26.png)


> yani structure şu şekilde : 
```
Source Code
      │
      ▼
Builder Stage
(Go compiler + go mod + go build)
      │
      ▼
Executable (product-catalog)
      │
      ▼
Release Stage
(Sadece executable + gerekli dosyalar)
      │
      ▼
Docker Image
      │
      ▼
docker run
      │
      ▼
Container (çalışan uygulama)
```

6. image'i build ediyoruz 

![alt text](/notes/images/image-28.png)

7. container oluşturalım

![alt text](/notes/images/image-29.png)