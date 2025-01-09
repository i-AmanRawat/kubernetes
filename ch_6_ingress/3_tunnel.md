# Tunnel to Minikube Cluster

In production, once you have an **Ingress** configured and have pointed your domain name to it (and perhaps its load balancer), you can access your application from anywhere in the world. The problem, however, is that we are using **Minikube** and the cluster is running locally, within an isolated virtual machine.

Fear not! Minikube provides a command to forward the ingress to your local machine.

## Assignment

### Step 1: Open the Tunnel

To forward the ingress controller's load balancer to your local machine, run the following command (you might need to enter your password):

```bash
minikube tunnel -c
```

### Step 2: Verify the Tunnel

You should keep this tunnel running in a separate terminal window as you will use it frequently in the course.

Once the tunnel is open, it will expose the **Ingress controller's load balancer** to your local machine at `127.0.0.1:80`. This allows us to access the services through the domains we configured earlier: `synchat.internal` and `synchatapi.internal`.

### Step 3: Test the Application

With the tunnel running, open a web browser and navigate to:

- [http://synchat.internal](http://synchat.internal) — You should see the **web app**.
- [http://synchatapi.internal](http://synchatapi.internal) — The root of the API should return a `404` status, but the `/healthz` endpoint should return a `200` status.

---

### Troubleshooting

- **If the tunnel doesn't work**: Ensure Minikube is running and your ingress is configured properly.
- **Accessing the API**: If `/healthz` does not return a `200`, check your API pod status and logs for errors.

```

This guide explains how to open a tunnel to your Minikube cluster, access the web app and API from your browser, and test the setup. It also includes troubleshooting tips.
```
