# Service Types

Take a look at the YAML that describes your `web-service`.

```bash
kubectl get svc web-service -o yaml
```

> **Note**: "svc" is a short-hand alias for "service", either will work in `kubectl`.

You should see a section that looks like this:

```yaml
spec:
  clusterIP: 10.96.213.234
  ...
  type: ClusterIP
```

We didn't specify a service type! Why is this here? Well, it's because **ClusterIP** is the default service type.

The `clusterIP` is the IP address that the service is bound to on the internal Kubernetes network. Remember how we talked about how pods get their own internal, virtual IP address? Well, services can too! However, `type: ClusterIP` is just one type of service! There are several others, including:

- **NodePort**: Exposes the Service on each Node's IP at a static port.
- **LoadBalancer**: Creates an external load balancer in the current cloud environment (if supported, e.g., AWS, GCP, Azure) and assigns a fixed, external IP to the service.
- **ExternalName**: Maps the Service to the contents of the `externalName` field (for example, to the hostname `api.foo.bar.example`). The mapping configures your cluster's DNS server to return a CNAME record with that external hostname value. No proxying of any kind is set up.

### How Do Service Types Build On Each Other?

The interesting thing about service types is that they typically build on top of each other. For example:

- **NodePort** is just a **ClusterIP** service with the added functionality of exposing the service on each node's IP at a static port (it still has an internal cluster IP).
- **LoadBalancer** is just a **NodePort** service with the added functionality of creating an external load balancer in the current cloud environment (it still has an internal cluster IP and node port).
- **ExternalName** is actually a bit different. All it does is a **DNS-level redirect**. You can use it to redirect traffic from one service to another.

### Which Type Should I Use?

Well, it depends on a lot of things:

- If you're working in a microservices environment where many services are only meant to be accessed **within the cluster**, then **ClusterIP** is going to be your go-to.
- **NodePort** and **LoadBalancer** are used when you want to **expose a service to the outside world**.
- **ExternalName** is primarily for **DNS redirects** (though, frankly, I've never used it).

We'll talk more about exposing services to the outside world in a later chapter.

```

```
