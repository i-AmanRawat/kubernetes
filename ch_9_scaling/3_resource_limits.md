# Resource Limits in Kubernetes

None of our current deployments have any **resource limits** set. While we have very little traffic, so it's not currently an issue, in a **production environment** we would want to set resource limits to ensure that our pods don’t consume excessive resources.

Without limits, a pod could potentially hog all the **CPU** and **RAM** on its node, which could suffocate other pods running on the same node. To prevent this, it's essential to configure **resource limits** for your deployments.

## Setting Resource Limits

We can define resource limits in the **deployment files** to prevent a pod from using too many resources. Here's an example of how to set resource limits for a container in a deployment file:

```yaml
spec:
  containers:
    - name: <container-name>
      image: <image-name>
      resources:
        limits:
          memory: <max-memory>
          cpu: <max-cpu>
```

### Resource Measurement Units

- **Memory** is measured in bytes. We can use the following suffixes to specify memory units:

  - `Ki` for **kilobytes**
  - `Mi` for **megabytes**
  - `Gi` for **gigabytes**

  Example: `512Mi` means 512 megabytes of memory.

- **CPU** is measured in cores, but Kubernetes allows us to specify **milli-cores** using the `m` suffix. For example:
  - `500m` means **500 milli-cores**, or **0.5 cores**.

## Assignment: Testing Resource Limits

It would be difficult to test resource limits with our **SynergyChat web application** since it has no production traffic. Instead, we'll use a custom application to test and debug the resource limits.

The `bootdotdev/synergychat-testcpu:latest` image on **Docker Hub** is a simple application that consumes as much **CPU power** as it can.

### Step 1: Create the Deployment YAML File

Create a new file called `testcpu-deployment.yaml` with the following content:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: synergychat-testcpu
  name: synergychat-testcpu
spec:
  replicas: 1
  selector:
    matchLabels:
      app: synergychat-testcpu
  template:
    metadata:
      labels:
        app: synergychat-testcpu
    spec:
      containers:
        - image: bootdotdev/synergychat-testcpu:latest
          name: synergychat-testcpu
```

### Step 2: Add the CPU Limit

Now, add a **CPU limit** of `50m` to the deployment (50 milli-cores). The updated deployment should look like this:

```yaml
spec:
  containers:
    - name: synergychat-testcpu
      image: bootdotdev/synergychat-testcpu:latest
      resources:
        limits:
          cpu: 50m
```

### Step 3: Apply the Deployment

Once the file is updated, apply the deployment with the following command:

```bash
kubectl apply -f testcpu-deployment.yaml
```

### Step 4: Verify Pod Status

Check that the pod is running:

```bash
kubectl get pod
```

It might take a minute or so, but soon you should see the pod's status. You can verify that the resource limits are applied properly by checking the pod's resource usage:

### Step 5: View Resource Usage

To see the resource usage of the pod, use:

```bash
kubectl top pod
```

If everything is working correctly, you should see that the pod is using **50 milli-cores** of CPU. Kubernetes throttles the pod to ensure that it does not exceed the specified CPU limit.

### Step 6: Run the Proxy

To access the Kubernetes API, run:

```bash
kubectl proxy
```

Once the proxy is running, you can use the CLI tool to submit HTTP tests.

## Conclusion

Setting resource limits ensures that your pods don't consume excessive resources and that other applications on the same node aren't starved of resources. It's important to carefully define these limits based on your workload's needs to prevent resource contention and ensure efficient scaling.

```

```
