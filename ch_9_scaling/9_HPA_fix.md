# HPA Fix: Reducing Resource Usage for the TestCPU Deployment

To prevent the `testcpu` deployment from consuming excessive resources on your machine, we will set a CPU resource limit and update the Horizontal Pod Autoscaler (HPA) to ensure it scales down properly.

### Assignment: Set Resource Limit and Update HPA

Follow these steps to reduce the resource usage and update the HPA configuration.

## Step 1: Set CPU Resource Limit for the `testcpu` Deployment

First, we need to set a CPU limit for the `testcpu` deployment. This will prevent the application from consuming too much CPU, which could impact the performance of your machine.

1. Open your `testcpu-deployment.yaml` file.
2. Under the `resources` section for the container, set the CPU limit to **`10m`** (10 milli-cores). The updated deployment should look like this:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: testcpu
spec:
  replicas: 1
  selector:
    matchLabels:
      app: testcpu
  template:
    metadata:
      labels:
        app: testcpu
    spec:
      containers:
        - name: testcpu
          image: bootdotdev/synergychat-testcpu:latest
          resources:
            limits:
              cpu: "10m" # Limit the CPU to 10 milli-cores
```

3. Apply the changes to your deployment:

```bash
kubectl apply -f testcpu-deployment.yaml
```

This will limit the `testcpu` pod to use at most **10 milli-cores** of CPU.

## Step 2: Update the HPA to Scale Down to 1 Pod

Next, we need to update the **Horizontal Pod Autoscaler (HPA)** to reflect the new resource limits. Specifically, we’ll set the **maxReplicas** to **1** so that the number of pods can no longer scale beyond one pod.

1. Open your `testcpu-hpa.yaml` file.
2. Update the `maxReplicas` to `1`:

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: testcpu-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: testcpu
  minReplicas: 1 # Keep the minimum number of replicas at 1
  maxReplicas: 1 # Set the maximum number of replicas to 1
  targetCPUUtilizationPercentage: 50 # Keep the target CPU utilization at 50%
```

3. Apply the updated HPA configuration:

```bash
kubectl apply -f testcpu-hpa.yaml
```

## Step 3: Monitor the Scaling Behavior

Now that we’ve set the resource limit and updated the HPA, let's observe how the deployment scales.

### Check Pods Status

Run the following command to see the status of the pods:

```bash
kubectl get pods
```

Since we have set the maximum number of replicas to **1**, the number of pods should not increase beyond that.

### Check CPU Usage

To monitor the CPU usage of the pods, use the following command:

```bash
kubectl top pods
```

The output will show the CPU utilization of each pod. If the CPU utilization drops below the target of **50%**, the autoscaler may scale down the pod, as it is now limited to a maximum of one pod.

### Check the Status of the HPA

Finally, check the status of the **Horizontal Pod Autoscaler**:

```bash
kubectl get hpa
```

This will display the current state of the HPA and show if any scaling actions are being taken.

## Conclusion

By setting the **CPU resource limit** to **10m** and updating the **maxReplicas** in the HPA to **1**, we've successfully constrained the `testcpu` deployment’s resource usage and prevented it from scaling out beyond a single pod.

You should now see the pod scale down or stay at 1 pod, depending on the CPU usage, and the deployment will no longer consume an excessive amount of resources on your machine.

```

### Key Points:
- **Setting a CPU resource limit** to prevent excessive resource usage.
- **Updating the HPA** to ensure it scales down to 1 replica.
- **Monitoring** commands to observe scaling behavior (`kubectl get pods`, `kubectl top pods`, `kubectl get hpa`).
```
