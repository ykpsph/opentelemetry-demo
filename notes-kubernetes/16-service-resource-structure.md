##  Detailed Review of Service Resource Structure


>>> *apiVersion*, *kind*, *metadata*, *labels* these are common fields. But in the **spec** we have the important part of the service resource.
>>> **type** defines the type of the service. (in general there are 3 types of services : ClusterIP, Nodeport and LB) -- they are for internal access / external access.
>>> then we provide **port** for the service. This port is not the *container* port, so we can provide any port for the service, `<name-of-the-service>:<port-of-the-service>`
>>> **targetPort** is the port of the *container*.
>>> **selector** is the most important one. A service identifies pods using labels, service will look at the pod using a particular label. For service to know which label it has to look at, we write **selector** and within the **selector** we provide the **label** AND this **label** is the same one as the one in the deployment template.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: opentelemetry-demo-adservice
  labels:
    opentelemetry.io/name: opentelemetry-demo-adservice
    app.kubernetes.io/instance: opentelemetry-demo
    app.kubernetes.io/component: adservice
    app.kubernetes.io/name: opentelemetry-demo-adservice
    app.kubernetes.io/version: "1.12.0"
    app.kubernetes.io/part-of: opentelemetry-demo
spec:
  type: ClusterIP
  ports:
    - port: 8080
      name: tcp-service
      targetPort: 8080
  selector:
    opentelemetry.io/name: opentelemetry-demo-adservice
```