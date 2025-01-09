# Breaking the Limits: Testing Memory Crashes in Kubernetes

In the previous steps, we observed that the **CPU** of the application was throttled, but we never explicitly told the `testcpu` application how much CPU to use. This is because, generally speaking, applications don't inherently know how much CPU they should use—they simply use as much as they can while performing computations.

However, **memory** behaves differently. Applications allocate memory based on various factors, and while an application can have its CPU throttled and "go slower," if the application runs out of memory, it will **crash**.

Now, let's test how an application behaves when it exceeds its memory limit.

## Step 1: Update the `MEGABYTES` Environment Variable

We will update the `MEGABYTES` environment variable for the `testram` application to allocate **500MB** of memory instead of 200MB.

1. Update the environment variable in your **ConfigMap** to `500`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: synergychat-testram-configmap
data:
  MEGABYTES: "500"
```

2. Apply the updated ConfigMap:

```bash
kubectl apply -f testram-configmap.yaml
```

## Step 2: Delete the `testram` Pod

In order for the new environment variable to take effect, we need to delete the existing pod. Kubernetes will automatically create a new pod using the updated ConfigMap:

```bash
kubectl delete pod -l app=synergychat-testram
```

## Step 3: Verify the Pod Status

After deleting the pod, Kubernetes will recreate it. The pod should eventually **crash** due to the excessive memory allocation. You can verify the status of the pod:

```bash
kubectl get pods
```

Look for the pod with the `CrashLoopBackOff` status, indicating that it failed to start properly due to memory exhaustion.

## Step 4: Describe the Pod

To get more information about why the pod crashed, describe the individual pod:

```bash
kubectl describe pod <pod-name>
```

In the output, look for the following section:

```plaintext
Containers:
  synergychat-testram:
    Container ID:   docker://<container-id>
    Image:          bootdotdev/synergychat-testram:latest
    Image ID:       docker-pullable://bootdotdev/synergychat-testram@<sha256-hash>
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       OutOfMemoryKilled
```

### Explanation:

- **State: Waiting**
  - The pod is in a waiting state because it crashed during the startup process.
- **Reason: CrashLoopBackOff**
  - This indicates that Kubernetes tried to start the pod multiple times but was unsuccessful, so it has started backing off from further attempts.
- **Last State: Terminated**
  - This shows that the pod was previously terminated.
- **Reason: OutOfMemoryKilled**
  - The application crashed because it tried to use more memory than the Kubernetes node could allocate, resulting in an **OutOfMemory** error.

## Conclusion

In this exercise, we have tested how an application behaves when it exceeds its **memory limit** in Kubernetes. The pod **crashed** because the application tried to allocate more memory than what was allowed by the **ConfigMap** and **Kubernetes resource limits**.

The key takeaway is that unlike CPU throttling, which slows down the application, exceeding the memory limit causes the pod to **crash**. By setting appropriate memory limits, you can prevent this behavior in a production environment.

```

### Key Changes:
- Added headings and steps for clear, easy-to-follow instructions.
- Clarified the **MEGABYTES** update and **ConfigMap** process.
- Provided step-by-step actions for deleting the pod and examining its crash logs.
- Included detailed output to look for in the `kubectl describe` command, along with an explanation of the crash reasons.
```
