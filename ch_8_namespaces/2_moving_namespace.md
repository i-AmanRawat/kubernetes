# Moving Namespaces in Kubernetes

Up until this point, we've been working in the **default namespace**. When using `kubectl` commands, you can specify the namespace with the `--namespace` or `-n` flag. If you don't specify a namespace, it will default to the `default` namespace.

## Example of Using `kubectl` in the Default Namespace

```bash
wagslane@MacBook-Pro courses % kubectl get pod
NAME                                  READY   STATUS    RESTARTS   AGE
synergychat-api-646c6fd585-dk5db      1/1     Running   0          28m
synergychat-crawler-cd4947995-tcrkn   3/3     Running   0          39m
synergychat-web-846d86c444-d9c8q      1/1     Running   0          28m
synergychat-web-846d86c444-sk6n4      1/1     Running   0          28m
synergychat-web-846d86c444-w2pqg      1/1     Running   0          28m
```

Without specifying the namespace, this shows pods in the **default namespace**.

### Example of Using `kubectl` in the `kube-system` Namespace

```bash
wagslane@MacBook-Pro courses % kubectl -n kube-system get pod
NAME                               READY   STATUS    RESTARTS     AGE
coredns-5d78c9869d-jwcbr           1/1     Running   0            4d
etcd-minikube                      1/1     Running   0            4d
kube-apiserver-minikube            1/1     Running   0            4d
kube-controller-manager-minikube   1/1     Running   0            4d
kube-proxy-j2ssm                   1/1     Running   0            4d
kube-scheduler-minikube            1/1     Running   0            4d
storage-provisioner                1/1     Running   1 (4d ago)   4d
```

The `kube-system` namespace is where all the core Kubernetes components live, and it is created automatically when you install Kubernetes. **Avoid modifying this namespace** unless absolutely necessary.

## Making a New Namespace

If you have a small cluster with only a few applications, the **default namespace** may suffice. However, in large clusters with many applications or multiple teams working on different services, using namespaces helps organize and isolate resources.

### Creating a New Namespace

For the sake of this exercise, let's assume that we have a development team responsible for the **crawler service** at SynergyChat, and they need their own namespace.

1. **Create a New Namespace**  
   To create a new namespace called `crawler`, run:

   ```bash
   kubectl create ns crawler
   ```

2. **Verify Namespace Creation**  
   Check if the new namespace was created successfully:

   ```bash
   kubectl get ns
   ```

   You should see `crawler` listed along with other namespaces in the cluster.

### Moving Resources to the New Namespace

Now, let's move the crawler resources (like deployments, services, configmaps, etc.) to the newly created `crawler` namespace.

1. **Update the Resources**  
   Add `namespace: crawler` to the **metadata** section of each of the crawler resources (such as `Deployment`, `Service`, and `ConfigMap`). Here's an example of what a deployment might look like after modification:

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: synergychat-crawler
     namespace: crawler # Add this line
   spec: ...
   ```

2. **Apply the Updated Resources**  
   Apply the changes by running the following commands for each resource:

   ```bash
   kubectl apply -f <crawler-deployment-file>.yaml
   kubectl apply -f <crawler-service-file>.yaml
   kubectl apply -f <crawler-configmap-file>.yaml
   ```

   Interestingly, these resources will be "created" instead of "updated." This is because Kubernetes uses the **name + namespace** combination as a unique identifier for resources. By changing the namespace, Kubernetes treats it as a new resource.

3. **Verify Resources in the New Namespace**  
   Once you've applied the resources in the `crawler` namespace, ensure they're deployed correctly:

   ```bash
   kubectl -n crawler get pods
   kubectl -n crawler get svc
   kubectl -n crawler get configmaps
   ```

4. **Delete Old Resources in the Default Namespace**  
   Now, delete the old resources in the **default namespace**:

   ```bash
   kubectl delete deployment <deployment-name>
   kubectl delete service <service-name>
   kubectl delete configmap <configmap-name>
   ```

### Running Tests

After the resources have been moved to the new namespace, you can run the following command to start the Kubernetes proxy:

```bash
kubectl proxy
```

Then, use the **CLI tool** to submit HTTP tests and verify that everything is working as expected.

## Conclusion

Namespaces are an effective way to organize and isolate resources in a Kubernetes cluster, especially when multiple teams or environments are involved. By moving resources into separate namespaces, you can ensure that each team or service has its own isolated environment while maintaining the overall efficiency of the cluster.

```

```
