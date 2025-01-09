# Deploying SynergyChat

In this section, you'll deploy the **SynergyChat** web application to your Kubernetes cluster. SynergyChat is a cutting-edge chat app that provides data-driven insights with AI and Web 3 infrastructure. We'll focus on deploying the application to your local Minikube Kubernetes cluster.

## Step 1: Deploying an Image

Kubernetes uses **deployments** to manage application instances (pods) and scaling. To create a deployment for the SynergyChat web app, run the following command:

```bash
kubectl create deployment synergychat-web --image=docker.io/bootdotdev/synergychat-web:latest
```

### Breakdown:

- **synergychat-web**: The name of the deployment. You can name it anything, but this is a convention.
- **docker.io/bootdotdev/synergychat-web:latest**: The Docker image for SynergyChat. This will pull the latest version of the web app from Docker Hub.

After running this command, Kubernetes will automatically pull the image and create the deployment for you.

## Step 2: Viewing Deployments

To verify the deployment was successful, run:

```bash
kubectl get deployments
```

This will show you the deployments currently active in your cluster. Look for the `synergychat-web` deployment to confirm it was created successfully.

### Example Output:

```bash
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
synergychat-web    1/1     1            1           2m
```

- **READY**: Indicates how many pods in the deployment are running.
- **UP-TO-DATE**: How many replicas are currently updated to the desired version.
- **AVAILABLE**: How many replicas are available for use.

## Step 3: Accessing the Web Page

By default, Kubernetes pods run in an isolated network and aren't accessible externally. You'll need to use **port-forwarding** to access the SynergyChat web page locally.

### 1. Check Pod Status

First, find the pod created by your deployment by running:

```bash
kubectl get pods
```

This will list all the pods running in your cluster. Look for the `synergychat-web` pod, and note its name.

### Example Output:

```bash
NAME                                   READY   STATUS    RESTARTS   AGE
synergychat-web-679cbcc6cd-cq6vx       1/1     Running   0          20m
```

### 2. Port Forwarding

Now, run the following command to forward traffic from port `8080` on your local machine to the pod's port `8080`:

```bash
kubectl port-forward PODNAME 8080:8080
```

Replace `PODNAME` with the actual name of the pod you got from the previous step (e.g., `synergychat-web-679cbcc6cd-cq6vx`).

### 3. Open the Web Page

Once port forwarding is active, open your browser and navigate to:

```
http://localhost:8080
```

You should see the **SynergyChat** webpage! While the forms on the page might not work yet (since we haven't set up all the resources), the page should load successfully.

## Step 4: Run and Submit HTTP Tests

To verify that everything is set up correctly, use the **Boot.dev CLI tool** to run HTTP tests. These tests will check that your port forwarding works and that the page loads correctly.

### Example Command:

```bash
bootdev cli http-tests
```

Once the tests pass, submit them to ensure your setup is complete.

---

By following these steps, you have successfully deployed the **SynergyChat** web service to your Kubernetes cluster and accessed it locally through port forwarding.

```

### Key Concepts:
- **Deployment**: A Kubernetes resource that manages the lifecycle of pods, including scaling and updates.
- **Pods**: The smallest deployable unit in Kubernetes, representing a running container.
- **Port Forwarding**: A method to access resources inside a Kubernetes cluster from your local machine by forwarding traffic from your local port to a port on a pod.

```
