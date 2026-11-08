##  Detailed Review of Deployment Resource Structure
> NOTE : kubernetes klasöründe her bir microservice için deploy.yaml ve svc.yaml dosyaları bulunmaktadır. Ayrıca complete-deploy.yaml 'da tüm bunların kombinasyonudur.
kubectl apply -f complete-deploy.yaml >>>  the complete project will be deployed on EKS.

>>> just like every resource in kubernetes, even a deployment resource will start with **apiVersion**. Using **apiVersion** and **kind**, kubernetes will understand what kind of resource it is. (because there are different type of resources in kubernetes, like deployment, serviceconfig, map ingress etc.)
```yaml
# Source: opentelemetry-demo/templates/component.yaml
apiVersion: apps/v1
kind: Deployment
```
***
<br>

>>> then comes the **metadata** information. some fields are mandatory like **name** (which is the name of the deployment). **labels** (labes are for identification of the deployment resource)
```yaml
metadata:
  name: opentelemetry-demo-adservice
  labels:
    opentelemetry.io/name: opentelemetry-demo-adservice
    app.kubernetes.io/instance: opentelemetry-demo
    app.kubernetes.io/component: adservice
    app.kubernetes.io/name: opentelemetry-demo-adservice
    app.kubernetes.io/version: "1.12.0"
    app.kubernetes.io/part-of: opentelemetry-demo
```
***
<br>

>>> **spec** differs from section to section in kubernetes. in **spec** we write deployment related stuff. 1. **replicas** (a deployment creates intermediate resource called *replicaset* which needs **replicas** information. That's why **replicas** is a mandatory field for kubernetes deployment.)
```yaml
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      
      opentelemetry.io/name: opentelemetry-demo-adservice
```
***
<br>

>>> **template** defines the Kubernetes pod related configuration. (in docker-compose we have docker related configuration, and in **template** we have kubernetes pod related configuration.)
> important : in the template we define *pod metadata* which is useful for `service discovery`. Every pod will have a label which will be used for `service` to identify the pods, even if the pod restarts or changes the IP address.
```yaml
template:
    metadata:
      labels:
        opentelemetry.io/name: opentelemetry-demo-adservice
        app.kubernetes.io/instance: opentelemetry-demo
        app.kubernetes.io/component: adservice
        app.kubernetes.io/name: opentelemetry-demo-adservice
```
> > > > > > important : `spec içindeki metadata'ya koyduğumuz label'lar service discover için` --- `en üste yazdığımız label'lar ise deployment resource'ların identification'ı için.`

***
<br>

>>> at the same level of template metadata, we define **spec** which is for the pod that gets created within the deployment. Keep in mind that the spec above is for deployment.
>>> In this **spec** we define *serviceAccountName*. 
>>> within the pod we define the *container's configuration*. A pod can have one or multiple containers. Here we define the *name* of the container, we define the location of the *image* which is the dockerhub location. we define on what *port* it returns. *env* variables and if you have any *volumes*. that's all. Which is same as docker-compose configuration.
```yaml
    spec:
      serviceAccountName: opentelemetry-demo
      containers:
        - name: adservice
          image: 'ghcr.io/open-telemetry/demo:1.12.0-adservice'
          imagePullPolicy: IfNotPresent
          ports:
            
            - containerPort: 8080
              name: service
          env:
            - name: OTEL_SERVICE_NAME
              valueFrom:
                fieldRef:
                  apiVersion: v1
                  fieldPath: metadata.labels['app.kubernetes.io/component']
            - name: OTEL_COLLECTOR_NAME
              value: 'opentelemetry-demo-otelcol'
            - name: OTEL_EXPORTER_OTLP_METRICS_TEMPORALITY_PREFERENCE
              value: cumulative
            - name: AD_SERVICE_PORT
              value: "8080"
            - name: FLAGD_HOST
              value: 'opentelemetry-demo-flagd'
            - name: FLAGD_PORT
              value: "8013"
            - name: OTEL_EXPORTER_OTLP_ENDPOINT
              value: http://$(OTEL_COLLECTOR_NAME):4318
            - name: OTEL_LOGS_EXPORTER
              value: otlp
            - name: OTEL_RESOURCE_ATTRIBUTES
              value: service.name=$(OTEL_SERVICE_NAME),service.namespace=opentelemetry-demo,service.version=1.12.0
          resources:
            limits:
              memory: 300Mi
          volumeMounts:
      volumes:
```