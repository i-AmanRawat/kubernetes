# Resource Requests in Kubernetes

In addition to **resource limits**, another critical concept in Kubernetes is **resource requests**.

## What is a Resource Request?

A **resource request** is the amount of a resource (like CPU or memory) that a pod requests from the node it's running on. It tells Kubernetes how much of a resource the pod **needs** to run effectively.

On the other hand, a **resource limit** is the maximum amount of a resource that a pod is allowed to use. If the pod exceeds this limit, it will be throttled (for CPU) or killed (for memory).

### Why Do We Need Resource Requests?

Let’s consider the following scenario where we have two nodes, each with 8 GB of RAM:

| **Node** | **RAM** |
| -------- | ------- |
| Node 1   | 8GB     |
| Node 2   | 8GB     |

We also have 4 pods, each requiring 3 GB of RAM:

| **Pod** | **Node** | **RAM** |
| ------- | -------- | ------- |
| Pod 1   | Node 1   | 3GB     |
| Pod 2   | Node 1   | 3GB     |
| Pod 3   | Node 2   | 3GB     |
| Pod 4   | Node 2   | 3GB     |

In this case, this setup is **valid** because each node is using only 6 GB of the 8 GB available. However, if we try to add another pod that requires 3 GB of RAM, we run into problems. Even though there’s 2 GB of free space on each node, Kubernetes would not allow the pod to be scheduled because it cannot guarantee that the pod will fit. If the pod requests more than the available space (e.g., 3 GB), it will crash.

This is where **resource requests** come in handy.

### How Resource Requests Solve This Problem

If we set a **resource request** of 3 GB per pod, Kubernetes will know that each pod needs 3 GB of RAM. If we try to add a new pod, Kubernetes will check the resource request and ensure there is enough available memory. If there is not enough free space on the node, Kubernetes will not schedule the pod on that node. Instead, it may look for another node in the cluster with sufficient resources.

### Example: Setting Resource Requests

Let’s experiment by setting an extremely high resource request for the **synergychat-testram** pods.

1. **Update the Deployment**: Add a resource request to the `resources` section of your deployment file. For example, we'll set the memory request to 40 GB:

   ```yaml
   resources:
     limits:
       memory: 40000Mi
     requests:
       memory: 40000Mi
   ```

2. **Apply the Deployment**:

   ```bash
   kubectl apply -f synergychat-testram-deployment.yaml
   ```

3. **Check Pod Status**: After applying the deployment, check the status of the pods:

   ```bash
   kubectl get pods
   ```

   You should see that the pod is in a **"Pending"** state. If you used a value like 40 GB for the request, it is likely that the pod won't be able to be scheduled, as no node will have that much available memory.

   > **Tip**: If the pod is in the "Pending" state, and you're feeling adventurous, try increasing the resource request even further!

4. **Describe the Pod**: To understand why the pod is in the "Pending" state, run the following command to get more details:

   ```bash
   kubectl describe pod <pod-name>
   ```

   Look for the **Events** section in the output to see why Kubernetes is unable to schedule the pod. The event messages will tell you if there is insufficient memory to meet the request.

### Running the Proxy and HTTP Tests

To test further, you can run the following commands:

1. Start a proxy server to your cluster:

   ```bash
   kubectl proxy
   ```

2. Use the CLI tool to run and submit the HTTP tests.

   > Note: This will only be useful if the pod is running. If the pod is pending, these tests might not execute properly.

### Key Takeaways:

- **Resource Requests** specify the amount of a resource that a pod requests from the node.
- **Resource Limits** specify the maximum amount of a resource a pod can consume.
- By setting **resource requests**, Kubernetes ensures that there are enough resources available for the pod to run, preventing scheduling issues and potential crashes due to resource exhaustion.

Setting appropriate resource requests helps Kubernetes manage resources more efficiently and prevents pods from being scheduled on nodes with insufficient resources.

```

### Key Concepts:
- **Resource Requests** ensure Kubernetes schedules pods only on nodes with enough available resources.
- **Resource Limits** prevent pods from consuming more resources than allowed, helping to protect the node and other running pods.
- Resource requests are critical for preventing **pod crashes** due to insufficient resources, especially when resources are constrained.
```
