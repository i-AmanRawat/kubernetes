# Horizontal Pod Autoscaling (HPA) for Web Application

In the previous example, we saw how an application with high CPU usage can quickly scale up from a single pod to multiple pods. Now, let's see how **Horizontal Pod Autoscaling (HPA)** works for an application with minimal compute demands, such as the `web` application.

### Assignment: Implementing HPA for the Web Application

We'll create a new HPA for the `web` application to see how it behaves with low resource utilization.

## Step 1: Remove the `replicas: 3` Line from the Web Deployment

First, we need to allow the new autoscaler to manage the number of replicas in the `web` deployment.

1. Open your `web-deployment.yaml` file.
2. Remove the `replicas: 3` line to allow HPA to control the scaling:

```yaml
# Remove or comment out the line below:
# replicas: 3
```

3. Apply the updated deployment:

```bash
kubectl apply -f web-deployment.yaml
```

This will remove the fixed number of replicas, allowing the autoscaler to manage the number of pods.

## Step 2: Create a New HPA for the Web Deployment

Next, make a copy of the `testcpu-hpa.yaml` file and rename it to `web-hpa.yaml`. Then, modify the following values:

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: web-hpa # Name of the HPA for the web application
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web # Target the 'web' deployment
  minReplicas: 1 # Minimum number of pods
  maxReplicas: 4 # Maximum number of pods
  targetCPUUtilizationPercentage: 50 # Target CPU utilization percentage
```

### Explanation:

- **`name`**: Set to `web-hpa` to give the HPA a unique name for the web application.
- **`scaleTargetRef.name`**: Set to `web` to target the `web` deployment.
- **`minReplicas`**: Set to `1` (minimum number of pods).
- **`maxReplicas`**: Set to `4` (maximum number of pods).
- **`targetCPUUtilizationPercentage`**: Set to `50%` (target average CPU utilization).

## Step 3: Apply the HPA

Now, apply the `web-hpa.yaml` file to create the new Horizontal Pod Autoscaler for the `web` deployment:

```bash
kubectl apply -f web-hpa.yaml
```

## Step 4: Monitor the HPA and Pod Scaling

To check if the HPA is scaling the `web` deployment, use the following commands:

### Check Pods Status

Run the following command to view the status of the pods:

```bash
kubectl get pods
```

You should see the current state of the pods in the `web` deployment. Since the application doesn't consume much CPU, you may not see any scaling unless there is a change in resource usage.

### Check CPU Usage

You can monitor the CPU usage of the pods by running:

```bash
kubectl top pods
```

This will show the CPU utilization of each pod in the `web` deployment. If the CPU usage is consistently low, you may not see any scaling happening.

### Check the Status of the HPA

To see the current state of the **Horizontal Pod Autoscaler**:

```bash
kubectl get hpa
```

This will display information about the `web-hpa` and whether it is scaling up or down based on CPU utilization.

## Conclusion

In this exercise, we applied **Horizontal Pod Autoscaling** to the `web` application. Since the application is not CPU-intensive, you may not see significant scaling. However, if CPU usage increases (e.g., under load), the HPA will automatically scale the number of pods within the defined range to maintain an average CPU utilization of **50%**.

This demonstrates how HPA can help efficiently manage resources even for applications with minimal compute needs.

```

### Key Points:
- **Step-by-step process** for removing the fixed replica count and setting up the HPA for the `web` application.
- **Updated YAML configuration** for the new HPA.
- **Monitoring commands** (`kubectl get pods`, `kubectl top pods`, `kubectl get hpa`) to watch the scaling behavior.
```
