# Storage in Kubernetes

By default, containers running in pods on Kubernetes have access to the filesystem, but there are significant limitations to this when it comes to persistence. The filesystem inside the container is **ephemeral**, meaning that when a pod is terminated or deleted, its filesystem is also wiped clean.

### Understanding Ephemeral Storage

The filesystem in a Kubernetes pod is local to the pod and **non-persistent**. This means if the pod restarts or is replaced (as in case of a crash or pod rescheduling), the data stored on that filesystem is lost.

This can be problematic for applications that need to persist data beyond the lifecycle of the pod, such as a database or an application like SynergyChat that stores chat messages on the filesystem.

## Assignment: Configuring the API Service to Use the Filesystem

The goal here is to configure the SynergyChat API service to save its data (the chat messages) to a file on the filesystem, so that even if the pod is restarted, the messages should remain accessible.

### Step 1: Update the API Service's ConfigMap

To persist chat data to the filesystem, we'll first update the `ConfigMap` for the API service to include a new environment variable.

1. Open the `api-service-configmap.yaml` file or create one if it doesn't exist.

2. Add the new environment variable `API_DB_FILEPATH`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: api-config
  namespace: default
data:
  API_DB_FILEPATH: "/var/lib/synergychat/api/db.json"
```

This configuration tells the SynergyChat API service that the database file should be saved to the specified path `/var/lib/synergychat/api/db.json`.

### Step 2: Update the API Deployment

Once the `ConfigMap` has been updated, we need to ensure that the deployment of the API service uses the new `API_DB_FILEPATH` variable.

1. Open the `api-deployment.yaml` file or update the relevant Kubernetes deployment configuration.

2. Modify the deployment to reference the updated `ConfigMap`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: synergychat-api
  namespace: default
spec:
  replicas: 3
  selector:
    matchLabels:
      app: synergychat-api
  template:
    metadata:
      labels:
        app: synergychat-api
    spec:
      containers:
        - name: synergychat-api
          image: synergychat-api:latest
          ports:
            - containerPort: 8081
          envFrom:
            - configMapRef:
                name: api-config
```

The key change here is the addition of the `envFrom` section that links the `api-config` ConfigMap, which will inject the `API_DB_FILEPATH` environment variable into the container.

### Step 3: Apply the Updated Configuration

After updating the `ConfigMap` and the `Deployment` configuration, apply the changes:

```bash
kubectl apply -f api-service-configmap.yaml
kubectl apply -f api-deployment.yaml
```

This will apply both the updated `ConfigMap` and the `Deployment`, so the API service will use the new environment variable for the file path.

### Step 4: Check Logs

To verify that everything is working as intended, you can check the logs of the new pod to see if it is using the filesystem as expected.

```bash
kubectl logs <api-pod-name>
```

Look for a log message that indicates the application is saving the messages to the specified file (`/var/lib/synergychat/api/db.json`).

### Step 5: Test the Persistent Storage

Now that the application is configured to save messages to a file, let's test the persistence:

1. Open the webpage at [http://synchat.internal](http://synchat.internal).
2. Create a few messages as different users and **refresh the page**. The messages should still be there because they are being saved in the server's memory.
3. Now, **delete the API pod** using the following command:

   ```bash
   kubectl delete pod <api-pod-name>
   ```

   Wait for Kubernetes to automatically replace the deleted pod with a new one.

4. **Refresh the page again**. This time, **the messages will be gone**!

### Why Are the Messages Gone?

This is because in Kubernetes, **the filesystem is ephemeral** by default. When the pod is deleted, the container's filesystem is also deleted, including the saved messages.

While the database file was configured to be saved on the filesystem (`/var/lib/synergychat/api/db.json`), that filesystem is part of the container itself. Once the pod is deleted, the filesystem is wiped clean, and all data (including the messages) is lost.

This behavior is part of the philosophy behind Kubernetes and containers in general: the application environment should be stateless and easily reproducible. Pods are intended to be disposable, and this helps in debugging and scaling applications effectively without worrying about leftover state between pod restarts.

---

## What's Next? Persistent Storage in Kubernetes

To avoid the problem of losing data when pods are deleted, we need to configure **persistent storage** in Kubernetes. This involves using **Persistent Volumes (PV)** and **Persistent Volume Claims (PVC)** to store data outside the pod, ensuring that the data persists even if the pod is deleted or rescheduled.

We'll cover that in the next section, where you'll learn how to set up persistent storage for your API service and make sure that your chat messages persist across pod restarts.

---
