# Change API Service

Remember how I said that **NodePort** and **LoadBalancer** services are used to expose services to the outside world? That's true, but in most cloud-based Kubernetes environments, you'll actually use an **Ingress** object to expose your services. The **Ingress** object not only exposes your service to the outside world, but also allows you to do things like:

- Host multiple services on the same IP address
- Host multiple services on the same port (path-based routing)
- Terminate SSL
- Integrate directly with external DNS and load balancers

Because we'll be setting up **Ingress** in the next chapter anyway, there's no reason to expose the **API service** with a **NodePort** service. Let's change it back to a **ClusterIP** service.

## Assignment

1. **Switch the `api-service` back to a ClusterIP service**. Here’s how the updated YAML should look:

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: api-service # Name of the service
   spec:
     selector:
       app: synergychat-api # Target the proper API pods using the app label
     ports:
       - protocol: TCP # Use TCP for HTTP traffic
         port: 80 # Port the service will listen on
         targetPort: 8080 # Port on the API pods
     type: ClusterIP # ClusterIP makes it accessible only inside the cluster
   ```

2. **Apply the service**:

   ```bash
   kubectl apply -f api-service.yaml
   ```

3. **Run the proxy**:

   ```bash
   kubectl proxy
   ```

Now, the API service will be available only inside the cluster, and we will configure an Ingress object in the next chapter to expose it properly.

```

```
