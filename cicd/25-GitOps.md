# Neden CD ?
- CI'ın sonunda Kubernetes manifestleri güncelleniyor. Örneğin başlangıçta image: dscc86y/product-catalog:v1 iken CI yeni image oluşturdu: dscc86y/product-catalog:v2 ve manifesti : image: dscc86y/product-catalog:v2 şeklinde güncelledi ve CI tamamlandı. Ama henüz Kubernetes cluster'da v2 çalışmıyor. İşte burada CD başlıyor.

- CD'nin görevi şu : güncellenmiş Kubernetes manifestini alıp Kubernetes cluster'a uygulamak.
```yaml
                 CI
Developer ──────────────────→ Git
                              │
                              │ manifest v2
                              ↓
                         CD / Argo CD
                              │
                              ↓
                       Kubernetes Cluster
                              │
                              ↓
                       product-catalog:v2
```

## GitOps & ArgoCD
GitOps'un temel fikri: Kubernetes'in olması gereken state'ini Git'te tut ve CD aracı Git'i takip ederek cluster'ı bu state'e getir.

ArgoCD'nin temel görevi ise :
```yaml
Git
 │
 │ v1 → v2
 ↓
Argo CD
 │
 │ "Değişiklik var!"
 ↓
Kubernetes
 │
 ↓
v2 deploy edilir
```

# TÜM ZİNCİR 
```yaml
Developer changes code
        ↓
     CI starts
        ↓
Build application
        ↓
Create Docker image
        ↓
Push image to Docker Hub
        ↓
Update Kubernetes manifest
        ↓
       Git
        ↓
      Argo CD
        ↓
 Kubernetes cluster
        ↓
Deploy new version
```
- bu niye var peki ? :D kubectl apply -f ... yapmayalım diye
- important : tabii ki bunun olabilmesi için CI Git'teki desired state'i değiştirirse Argo CD cluster'ı o desired state'e getirir.