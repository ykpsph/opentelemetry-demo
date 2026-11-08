# CI / CD
CI = kodu otomatik olarak test etme ve build etme süreci. CI = Build -> Test -> Image oluşturma
CD = başarılı olan yeni versiyonu otomatik olarak deploy etme süreci. CD = Deployment

### CI tam olarak ne yapıyor? 
- Diyelim ki bir developer, Pull Request oluşturdu
```
Pull Request
     ↓
Checkout code
     ↓
Unit tests
     ↓
Static code analysis
     ↓
Build application
     ↓
Create Docker image
     ↓
Scan Docker image
     ↓
Push Docker image
     ↓
Update Kubernetes manifest
```

1. **Checkout code :** CI sistemi önce kodu Git repository'den alıyor. CI sistemi yeni bir VM üzerinde kodu çalıştırıyor. GitHub Actions bunu otomatik olarak oluşturabilir; Jenkins'te ise önceden hazırlanmış makineler kullanılabilir.
2. **Unit tests :** Kod checkout edildikten sonra testler çalıştırılıyor:
3. **Static code analysis :** Burada programı çalıştırmadan kodu analiz ediyoruz.
4. **Build application :** Sonra application build ediliyor:
5. **Create Docker image :** Docker Image oluşturma
6. **Scan Docker image :** Sonra image security açısından taranıyor.
7. **Push Docker image :** Image başarılıysa registry'ye gönderiliyor.
8. **Update Kubernetes manifest :** CI son aşamada Git repository'deki Kubernetes manifestini güncelliyor.

### Peki CD ne yapıyor?
- CD'nin amacı: Yeni image'ı Kubernetes cluster'a deploy etmek.
```
                    CI                         CD
                    │                          │
Developer → GitHub → Test → Build → Image → Push
                                             │
                                             ↓
                                      Update K8s manifest
                                             │
                                             ↓
                                           Argo CD
                                             │
                                             ↓
                                          EKS/K8s
                                             │
                                             ↓
                                            Pods
```

# GitHub Actions nedir ?
- CI pipeline'ın hangi zamanda ve hangi adımlarla çalışacağını yöneten sistem

> GitHub Actions workflow'u nereden biliyor ? 
- .github/workflows/ci.yaml


## ci.yaml ne ?
- pipeline'ın talimatları gibi düşün

```yaml
name: Product Catalog CI

on:
  push:
  pull_request:

jobs:
  build:
    ...
```
- name : workflow'un adı
- on : workflow ne zaman çalışacak ?
- jobs : workflow hangi işleri yapacak ?


### Workflow ve Action aynı şey değil
- workflow senin oluşturduğun *ci.yaml* dosyası
- action ise workflow'un içinde kullandığın hazır parçalar
```yaml
Workflow
│
├── checkout action
├── Go setup action
├── Docker action
└── ...
```
and
```yaml
Workflow
│
├── Build Job
│    ├── Checkout
│    ├── Install Go
│    ├── Build
│    └── Unit Tests
│
├── Static Analysis Job
│    ├── Checkout
│    └── Static Analysis
│
└── Docker Job
     ├── Docker Build
     ├── Docker Scan
     └── Docker Push
```
### Runner nedir ?
- Senin workflow'unun çalışması için GitHub'ın sağladığı bir VM