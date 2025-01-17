# StatefulSet Implementation

![alt text](statefulset-headless.drawio.svg)

# Kubernetes StatefulSet
A StatefulSet is a Kubernetes workload API object used to manage stateful applications. Unlike a Deployment, a StatefulSet maintains a sticky identity for each of its pods. These pods are created from the same spec but are not interchangeable: each has a persistent identifier that it maintains across any rescheduling.

**Key Features of StatefulSet**

* Stable, Unique Network Identifiers:

  Each pod in a StatefulSet gets a unique, stable network identity. This is achieved through a combination of a stable hostname and a headless service.

* Stable, Persistent Storage:

    StatefulSets can provide stable storage using PersistentVolumeClaims (PVCs). Each pod gets its own PVC, which is not shared with other pods.

* Ordered, Graceful Deployment and Scaling:

    Pods in a StatefulSet are created, updated, and deleted in a specific order. This ensures that the application can handle the changes gracefully.

* Ordered, Automated Rolling Updates:

    StatefulSets provide ordered, automated rolling updates to ensure that the application remains available during updates.

Example: StatefulSet Configuration

Here is an example of a StatefulSet configuration for a simple web application:

**Headless Service**

First, create a headless service to manage the network identities of the pods:

```
apiVersion: v1
kind: Service
metadata:
  name: web
  labels:
    app: web
spec:
  ports:
    - port: 80
      name: web
  clusterIP: None
  selector:
    app: web
```

**StatefulSet**

Next, create the StatefulSet:

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "web"
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx
          ports:
            - containerPort: 80
          volumeMounts:
            - name: www
              mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
    - metadata:
        name: www
      spec:
        accessModes: [ "ReadWriteOnce" ]
        resources:
          requests:
            storage: 1Gi
```


Headless Service:

**clusterIP: None**: This makes the service headless, meaning it does not have a stable IP address.
selector: Selects the pods with the label app: web.
StatefulSet:

**serviceName**: Refers to the headless service created earlier.

**replicas**: Specifies the number of pod replicas.

**selector**: Selects the pods with the label app: web.

**template**: Defines the pod template.

**containers**: Specifies the container configuration.

**volumeMounts**: Mounts the persistent volume to the container.

**volumeClaimTemplates**: Defines the PVC template for each pod.

Summary

StatefulSet: Manages stateful applications with stable network identities and persistent storage.

Headless Service: Provides stable network identities for the pods.

Ordered Deployment and Scaling: Ensures that pods are created, updated, and deleted in a specific order.

Persistent Storage: Each pod gets its own PVC, ensuring stable storage.

By using StatefulSets, you can manage stateful applications in Kubernetes, ensuring stable network identities, persistent storage, and ordered deployment and scaling. This is particularly useful for applications like databases, where each instance needs a stable identity and storage.