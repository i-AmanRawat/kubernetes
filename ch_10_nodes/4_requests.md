# Kubernetes Resource Requests and Limits

When working with **pod autoscalers** in Kubernetes, one of the most critical aspects is setting **resource requests** and **limits** correctly. If these values are not well-tuned, you could end up with issues such as:

- Pods crashing due to insufficient resources
- Autoscalers scaling up too many pods unnecessarily

## Rule of Thumb for Resource Requests and Limits

Here’s a general guideline you can follow when setting your resource requests and limits:

- **Memory Requests**: ~10% higher than the average memory usage of your pods.
- **CPU Requests**: ~50% of the average CPU usage of your pods.
- **Memory Limits**: ~100% higher than the average memory usage of your pods.
- **CPU Limits**: ~100% higher than the average CPU usage of your pods.

### Why These Numbers?

#### 1. Memory Is Scarier Than CPU

Memory is a more critical resource to manage than CPU because:

- **Running out of CPU**: If your pods run out of CPU, they will just **slow down**. The application may be slower, but it won't crash.
- **Running out of Memory**: If your pods run out of memory, they will **crash**. Kubernetes will kill the pod and try to restart it, which can lead to instability.

For this reason, it's more important to **add a buffer** to your **memory requests** compared to CPU requests.

#### 2. Limits Are for Protection

**Limits** should be used as **safety nets** to prevent pods from consuming excessive resources. They act as upper bounds to ensure that:

- Pods won’t overwhelm the node by consuming all available resources.
- If your limits are being consistently reached, either:
  - **Increase the limits**, or
  - **Optimize the application code** to use fewer resources.

As a rule, **limits should always be higher than requests**, to give pods some room to operate within safe bounds.

#### 3. Requests Are for Scheduling

**Requests** are used by Kubernetes to decide how to **schedule pods** onto nodes in the cluster. Therefore, you want to:

- Set your **requests** high enough that when a pod is scheduled, it **has enough resources** to run.
- Avoid setting requests too high, as that can lead to **resource wastage**. If you set requests too high, Kubernetes might not schedule the pod, even though there might be sufficient resources available.

#### 4. It All Depends on Your Application

These guidelines are **just rules of thumb**. The right values for **requests** and **limits** depend on:

- **The specific behavior** of your application.
- **The resources it needs** at various load levels.
- **Monitoring and adjusting** based on actual usage patterns.

The numbers you should use for your application might be very different from these recommendations, so always understand how your application works before setting these values.

## Conclusion

Setting the right **resource requests** and **limits** is essential for Kubernetes autoscalers to function effectively and for pods to operate efficiently. Use the above rules as a starting point, but remember to monitor your application’s resource usage and adjust accordingly.

- **Memory** should always have a larger buffer compared to **CPU** to prevent crashes.
- **Limits** should be set higher than **requests** to ensure protection but not excessive resource allocation.
- Tune requests and limits based on how your application behaves under load.

```

### Key Concepts:
- **Memory vs CPU**: Memory is more critical than CPU; running out of memory causes crashes, while running out of CPU causes slowdowns.
- **Resource Requests**: These are used for **scheduling** the pods on nodes. Set them appropriately so the pod can run without wasting resources.
- **Resource Limits**: Set these higher than requests to prevent pods from consuming excessive resources, but adjust them if your application constantly hits them.
```
