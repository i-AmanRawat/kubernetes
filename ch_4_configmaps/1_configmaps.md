# Config Maps

There are several ways to manage environment variables in Kubernetes. One of the most common ways is to use **ConfigMaps**. ConfigMaps allow us to decouple our configurations from our container images, which is important because we don't want to have to rebuild our images every time we want to change a configuration value.

In a Dockerfile, we can set environment variables like this:

```Dockerfile
ENV PORT=3000
```

The trouble is, that means that everyone using that image will have to use port `3000`. It also means that if we want to change the port, we have to rebuild the image.

## Assignment

1. **First, let's take a closer look at our crashing pod and try to figure out why it's crashing.**  
   Run:

   ```bash
   kubectl get pods
   ```

2. Copy the pod name and then get the logs:

   ```bash
   kubectl logs <pod-name>
   ```

   You should see that a specific environment variable is missing! Let's fix that.

3. **Create a new file**. Let's call it `api-configmap.yaml`. Add the following YAML to it:

   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: synergychat-api-configmap
   data:
     API_PORT: "8080"
   ```

4. **Apply the config map**:

   ```bash
   kubectl apply -f api-configmap.yaml
   ```

   Now, we haven't yet connected the config map to our pod, so it should still be crashing. However, for now, let's just validate that the config map was created successfully:

   ```bash
   kubectl get configmaps
   ```

5. **Run the proxy**:

   ```bash
   kubectl proxy
   ```
