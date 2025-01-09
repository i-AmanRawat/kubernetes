# Services

We've spun up pods and connected to them individually, but that's frankly not super useful if we want to distribute real traffic across those pods. That's where **services** come in.

Services provide a **stable endpoint** for pods. They are an abstraction used to provide a stable endpoint and load balance traffic across a group of Pods. By "stable endpoint", I just mean that the service will always be available at a given URL, even if the pod is destroyed and recreated.

## Services in Kubernetes

### Assignment

Let's add a service for our 3 `synergychat-web` pods. If you don't have 3 pods running, edit the deployment to have 3 replicas.

1. **Create a file called `web-service.yaml`** and add the following:

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: web-service # We could call it anything, but this is a fine name
   spec:
     selector:
       app: synergychat-web # This is how the service knows which pods to route traffic to
     ports:
       - protocol: TCP # TCP will allow us to use HTTP
         port: 80 # This is the port that the service will listen on
         targetPort: 8080 # This is the port that the pods are listening on
   ```

````

This creates a new service called `web-service` with a few properties:

- It listens on **port 80** for incoming traffic.
- It forwards that traffic to pods that are listening on their **port 8080**.
- Its controller will continuously scan for pods matching the `app: synergychat-web` label selector and automatically add them to its pool.

2. **Create the service:**

   ```bash
   kubectl apply -f web-service.yaml
   ```

3. **Forward the service's port to your local machine** so we can test it out:

   ```bash
   kubectl port-forward service/web-service 8080:80
   ```

4. **Test the service**. Now, if you hit `http://localhost:8080` in your browser, you should see the web app! It's better this time around because now our requests are being load-balanced across 3 pods.

```
````
