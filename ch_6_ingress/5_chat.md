Certainly! Here's a step-by-step guide to complete your assignment and connect the web application front-end to the API via Kubernetes ConfigMaps:

### Step-by-Step Instructions

#### 1. **Create the ConfigMap**

To create a new ConfigMap for the web service and add the required environment variables (`WEB_PORT` and `API_URL`), follow these steps:

- Create a file named `web-configmap.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: web-config
  namespace: default
data:
  WEB_PORT: "8080"
  API_URL: "http://synchatapi.internal"
```

This YAML file defines a `ConfigMap` with two key-value pairs:

- `WEB_PORT`: The port where the web service is running (8080).
- `API_URL`: The URL of the API service that the front-end will connect to (`http://synchatapi.internal`).

#### 2. **Apply the ConfigMap**

Once the `web-configmap.yaml` file is ready, apply the ConfigMap to your cluster:

```bash
kubectl apply -f web-configmap.yaml
```

This command creates the ConfigMap in your Kubernetes cluster.

#### 3. **Update the Web Deployment**

Next, update the web application's deployment to use this new ConfigMap as environment variables. Find the YAML definition of the web application's deployment (e.g., `web-deployment.yaml`) and modify it to include the environment variables from the ConfigMap.

Example update to the `web-deployment.yaml` file:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: synergychat-web
  namespace: default
spec:
  replicas: 3
  selector:
    matchLabels:
      app: synergychat-web
  template:
    metadata:
      labels:
        app: synergychat-web
    spec:
      containers:
        - name: synergychat-web
          image: synergychat-web:latest
          ports:
            - containerPort: 8080
          envFrom:
            - configMapRef:
                name: web-config
```

This modification includes the following:

- The `envFrom` section references the `web-config` ConfigMap, automatically injecting the environment variables (`WEB_PORT` and `API_URL`) into the container.

#### 4. **Apply the Updated Deployment**

Apply the updated deployment configuration:

```bash
kubectl apply -f web-deployment.yaml
```

This will update the web service pods with the new environment variables.

#### 5. **Test the Web Application**

Now, with everything configured and applied to your cluster, you can open the web application in your browser:

- Go to [http://synchat.internal](http://synchat.internal).

If everything is configured correctly:

- You should see the **chat interface** and be able to enter a **username** and **message**, and send it.
- The web page should communicate with the **API** through the URL specified in the ConfigMap (`http://synchatapi.internal`), and the messages should be stored in the server's memory (since they’re not persisted).

#### 6. **Test Data Persistence**

- **Create a few messages as different users** and **refresh the page**. The messages should still be there, confirming that they’re being saved in the server's memory (since we're not using any external database yet).
- Now, **delete the API pod**:

  ```bash
  kubectl delete pod <api-pod-name>
  ```

- **Wait for Kubernetes to replace the deleted pod** with a new one. When the new pod is created, refresh the web page again.

#### 7. **Expected Behavior After Pod Restart**

- After the pod restart, if the messages are **still available** (even after refreshing), it means they are being stored **in the memory** of the API pod, and Kubernetes is managing the pod's lifecycle.

- If you **lose the messages** after the pod restart, that means the API is **not persisting** data, and the messages are stored **only in memory** (which will be cleared when the pod is recreated).

#### 8. **Answer the Question**

- If the messages persist after the pod is restarted, you can confidently say that the messages are stored in memory, as expected for this scenario.
- If the messages are lost, it’s an indicator that the system needs a **persistent storage solution** (e.g., a database or persistent volumes) to retain data beyond pod restarts.

---

### Conclusion

By completing the above steps, you’ve successfully:

1. Created a **ConfigMap** to manage environment variables.
2. Updated the **web deployment** to use the new ConfigMap.
3. Connected the front-end to the back-end API using the **API URL** defined in the ConfigMap.
4. Tested the persistence of the messages by deleting and replacing the API pod.

This process demonstrates how to manage configuration data in Kubernetes using ConfigMaps and connect different components of your application (front-end to back-end) via environment variables.
