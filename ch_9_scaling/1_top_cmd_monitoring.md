# Monitoring Pod Resources in Kubernetes

We've already learned how to look at logs for Kubernetes pods, but sometimes that's not enough when it comes to debugging. Sometimes, we need to know about the resources that a pod is using.

## Enabling the Metrics Server

To get resource metrics working, we need to enable the `metrics-server` addon in Minikube. Run the following command:

```bash
minikube addons enable metrics-server
```

## Checking the `metrics-server` Pod

After enabling the metrics server, take a look inside the `kube-system` namespace:

```bash
kubectl -n kube-system get pod
```

You should see a new `metrics-server` pod. It might take a couple of minutes to get started, but once that pod is ready, you should be able to run:

```bash
kubectl top pod
```

This command will show you the resource usage of each pod.

## Sample Output of `kubectl top pod`

You should see something like this:

```plaintext
NAME                                CPU(cores)   MEMORY(bytes)
synergychat-api-76b796b58d-x5wpk    1m           14Mi
synergychat-web-846d86c444-d9c8q    1m           15Mi
synergychat-web-846d86c444-sk6n4    1m           15Mi
synergychat-web-846d86c444-w2pqg    1m           15Mi
```

The `kubectl top` command (similar to the Unix `top` command) will show you the resources that each pod is using. In the example above, each pod is using:

- **1 milliCPU** (`1m`)
- **15 megabytes of memory** (`15Mi`)

## Running a Proxy

To access the Kubernetes API from your local machine, run the following command:

```bash
kubectl proxy
```

Once the proxy is running, you can submit HTTP tests using the CLI tool.

```

```
