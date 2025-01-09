# Crawler Service

Finally, let's add a service for our **crawler application**. Just like the **API service** will need to be available to the front-end (which is served to a client outside the cluster), the **crawler service** will need to be available to the **API service** (internal to the cluster only).

## Assignment

You know how to do this by now! Here's the step-by-step guide:

1. **Create a new service** called `crawler-service`. Make sure it targets the proper crawler pods. This should be a **ClusterIP** service type because it’s only needed internally within the cluster.

   Here’s the structure for the service:

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: crawler-service # Name of the service
   spec:
     selector:
       app: synergychat-crawler # Target the proper crawler pods using the app label
     ports:
       - protocol: TCP # Use TCP for communication
         port: 8080 # Port the service will listen on
         targetPort: 8080 # Port on the crawler pods
     type: ClusterIP # ClusterIP makes it accessible only inside the cluster
   ```

2. **Apply the service**:

   ```bash
   kubectl apply -f crawler-service.yaml
   ```

3. **When your service is live**, you can run the proxy:

   ```bash
   kubectl proxy
   ```

The crawler service should now be live and accessible internally within the cluster, allowing the **API service** to communicate with it.

By using a **ClusterIP** service type, the crawler service remains internal, ensuring that only other services within the cluster can access it.

```

```
