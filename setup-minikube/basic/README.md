# Deployment

The following is a deployment example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-deployment
  labels:
    app: mongo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongodb
          image: mongo:5.0
          ports:
            - containerPort: 27017

---
```

The main section of any deployment is the one that define the **blueprint** which is whithin the spec: the **template**. Basically it's a configuration for the **pod** within the configuration of the deployments ( show the two layer abstraction before the container itself ). It has it's one metadata and spec itself.

Within **containers** we define the reference image for that given container, important to follow a tag from the hub of the container registry of use ( in this case, docker ), and the port the service actually uses.

## Labels

On kubernetes you can give any component a key value pair label, so you label anything from pod to deployment to configmap, etc.

And labels are basically additional identifiers to the components in addition to the name, for example.

They are usefull for identifying pods replicas (that have different names) by the specified label.

> For pods (template), "labels" is a required field

For other components of deployments labels are optional, but it's a good practice to set them.

When we create pods replicas, the deployments know the pods that belongs to it based on the **selector.matchLabels** spec.

The **replicas** define how many pods we want in a given cluster

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo-service
spec:
  selector:
    app: mongo
  ports:
    - protocols: TCP
      port: 80
      targetPort: 27017
```

The **selector** for a service is needed because it needs to foward the request the an specified pod and the selector is how it knows which pods belongs to it and which ones it should foward the request to.

The por is arbitrary, but the targetPort has to be the one the container listens to (containerPort).

Request -> (port 80) Mongo Service (targetPort 27017) -> Pod (containerPort 27017)

## Web service

(external service config)

## Service key value from Config

# Deploy

> kubectl apply -f .\config\mongo-config.yaml
> kubectl apply -f .\config\mongo-secret.yaml
>
> # created
>
> kubectl apply -f mongo.yaml
> kubectl apply -f webapp-service.yaml
