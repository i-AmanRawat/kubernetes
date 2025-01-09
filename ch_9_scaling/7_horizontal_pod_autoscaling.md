# Horizontal Pod Autoscaling (HPA) in Kubernetes

A **Horizontal Pod Autoscaler (HPA)** automatically scales the number of Pods in a Deployment based on observed **CPU utilization** or other custom metrics. It’s common to have a low number of pods in a deployment initially, and then let Kubernetes scale up the number of pods automatically as the **CPU usage** increases.

### Assignment: Implementing Horizontal Pod Autoscaling

Let's go through the steps to implement an **HPA** for the `testcpu` application.

## Step 1: Remove the `replicas` Field from the Deployment

First, delete the `replicas: 1` line from the `testcpu` deployment. This ensures that the **Horizontal Pod Autoscaler** will have full control over the number of pods in the deployment.

1. Open your `testcpu-deployment.yaml` file.
2. Remove the `replicas: 1` line.
3. Apply the updated deployment:

```bash
kubectl apply -f testcpu-deployment.yaml
```

## Step 2: Create the Horizontal Pod Autoscaler (HPA)

Next, create a new YAML file called `testcpu-hpa.yaml`. In this file, we will define the Horizontal Pod Autoscaler with the following configuration:

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: testcpu-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: testcpu # Name of the testcpu deployment
  minReplicas: 1 # Minimum number of replicas
  maxReplicas: 4 # Maximum number of replicas
  targetCPUUtilizationPercentage: 50 # Target CPU usage percentage
```

### Explanation:

- **`scaleTargetRef.name`**: The name of the deployment to scale. This should be set to `testcpu`.
- **`minReplicas`**: The minimum number of pods to run, set to `1`.
- **`maxReplicas`**: The maximum number of pods to scale up to, set to `4`.
- **`targetCPUUtilizationPercentage`**: The target CPU utilization percentage for the deployment. This is set to `50%`, meaning the autoscaler will try to adjust the number of pods to keep the average CPU usage at or around **50%**.

## Step 3: Apply the HPA

After creating the `testcpu-hpa.yaml` file, apply it to your Kubernetes cluster:

```bash
kubectl apply -f testcpu-hpa.yaml
```

## Step 4: Monitor the Autoscaler and Pod Scaling

Once the HPA is applied, you can watch the number of pods scale up or down based on CPU utilization. Run the following commands every few seconds to observe the scaling in action:

### Check Pods Status

```bash
kubectl get pods
```

This command will show the current state of all pods in the deployment. You should see the number of pods change as CPU utilization increases or decreases.

### Check CPU Usage

```bash
kubectl top pods
```

This will display the CPU usage of each pod. As the average CPU usage approaches or exceeds the target of 50%, the number of pods should increase.

### Check the Current State of the HPA

To see the status of the Horizontal Pod Autoscaler, use the following command:

```bash
kubectl get hpa
```

This will show you the current **minReplicas**, **maxReplicas**, **current CPU usage**, and the number of pods that the HPA has scaled to.

## Conclusion

In this exercise, we’ve set up a Horizontal Pod Autoscaler for the `testcpu` application. The autoscaler will monitor the CPU usage of the pods and scale the number of pods up or down as needed to maintain an average CPU utilization of **50%**.

The HPA is a powerful tool that helps you automatically adjust resources based on demand, ensuring that your application scales efficiently in response to load.

```

### Key Changes:
- **Step-by-step instructions** to modify the deployment and set up the HPA.
- **YAML snippets** for creating the HPA and applying the changes.
- **Clarified explanations** for each part of the configuration.
- **Commands** to monitor the autoscaling in action.
```
