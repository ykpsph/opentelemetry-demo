- ArgoCD installation tamamlandıktan sonra tüm pod'ların running olması için bekle
```yaml
>_ kubectl get pods -n argocd
```

- eğer AWS ortamındaysan ArgoCD'yi dışarıya erişime açmalısın. type'ı ClusterIP'den LoadBalancer'a çek ya da   
```yaml
>_ kubectl edit svc argocd-server -n argocd
```

- local'inde çalışıyorsan, port-forward yapmayı unutma : 
```yaml
>_ kubectl port-forward svc/argocd-server -n argocd 8080:443
```

- ArgoCD admin şifresi al : 
```yaml
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d
```


![argocd](/notes/images/image-19.png)

- application name
- sync policy - enable auto sycn and self heal
- repository url
- path
- namespace (default)


![argocd-final](/notes/images/image-21.png)