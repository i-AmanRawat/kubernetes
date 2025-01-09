# Limits - RAM in Kubernetes

We’ve successfully throttled the **CPU** usage of our `testcpu` pod, but what about **RAM**? Now, let's focus on setting a **memory limit** for our pods.

## Step 1: Create the Deployment YAML File

Create a new file called `testram-deployment.yaml` with the following content:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: synergychat-testram
  name: synergychat-testram
spec:
  replicas: 1
  selector:
    matchLabels:
      app: synergychat-testram
  template:
    metadata:
      labels:
        app: synergychat-testram
    spec:
      containers:
        - image: bootdotdev/synergychat-testram:latest
          name: synergychat-testram
```

````

### Step 2: Add a Memory Limit

Now, we need to set a **memory limit** for the pod. Add a memory limit of **256Mi** (256 Megabytes) to the deployment. Here's how you can update the `testram-deployment.yaml`:

```yaml
spec:
  containers:
    - name: synergychat-testram
      image: bootdotdev/synergychat-testram:latest
      resources:
        limits:
          memory: 256Mi
          cpu: 100m # optional, depending on whether you want to set a CPU limit as well
```

### Step 3: Create a ConfigMap

Next, create a **ConfigMap** named `testram-configmap.yaml` that specifies a single environment variable, `MEGABYTES`. This will instruct the application to allocate **200MB** of memory.

Create the ConfigMap file:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: synergychat-testram-configmap
data:
  MEGABYTES: "200"
```

### Step 4: Update the Deployment to Use the ConfigMap

Now, update the deployment in the `testram-deployment.yaml` to use the newly created **ConfigMap**. You’ll need to reference the environment variable from the ConfigMap in your container configuration:

```yaml
spec:
  containers:
    - name: synergychat-testram
      image: bootdotdev/synergychat-testram:latest
      resources:
        limits:
          memory: 256Mi
          cpu: 100m
      env:
        - name: MEGABYTES
          valueFrom:
            configMapKeyRef:
              name: synergychat-testram-configmap
              key: MEGABYTES
```

### Step 5: Apply Both Files

Apply the deployment and the ConfigMap:

```bash
kubectl apply -f testram-deployment.yaml
kubectl apply -f testram-configmap.yaml
```

### Step 6: Verify Pod Health

After applying the changes, check the status of the pod:

```bash
kubectl get pods
```

It may take a minute or two for the pod to be scheduled and started. Once it's running, you should be able to see its resource usage.

### Step 7: Monitor Memory Usage

To check the memory usage of the pod, run:

```bash
kubectl top pod
```

You should see that the pod is using a little over **200 megabytes** of memory but not exceeding the **256Mi** limit. Kubernetes will ensure that the memory usage doesn't go beyond the specified limit.

## Conclusion

Setting **memory limits** in Kubernetes ensures that your pods do not consume more resources than intended. By testing the limits with both CPU and RAM, you can prevent resource contention and ensure that the application behaves as expected in a controlled environment.

```

### Key Changes:

- Added clearer headings to guide the reader.
- Included the step-by-step instructions for creating and applying both the **Deployment** and the **ConfigMap**.
- Clarified the memory limit example and how the application utilizes the **ConfigMap**.

This should make the process easier to follow for testing **RAM limits** in a Kubernetes deployment.
```
````
