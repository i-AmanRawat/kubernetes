# API Service

As you know, the **web service** in the SynergyChat system serves the front-end assets (HTML, CSS, and JavaScript) to the user's browser. The **api service** is responsible for handling requests from the front-end and returning data from the database.

Let's add a **"NodePort"** type service to expose the API service to the outside world.

## Assignment

1. **Create a copy of your `web-service.yaml` file** and name it `api-service.yaml`. Change the following:

   - The name should be `api-service`
   - Make sure it selects pods using the `app: synergychat-api` key
   - Add `type: NodePort` to the `spec` section
   - Add `nodePort: 30080` to the first object in the `ports` list

   Here is the modified YAML structure for reference:

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: api-service # Name of the service
   spec:
     selector:
       app: synergychat-api # Select pods using the app label
     ports:
       - protocol: TCP # TCP will be used for HTTP traffic
         port: 80 # The port the service will listen on
         targetPort: 8080 # The port the pods are listening on
         nodePort: 30080 # Exposing the service on port 30080 of each node
     type: NodePort # This exposes the service outside the cluster
   ```

````

2. **Apply the service**:

   ```bash
   kubectl apply -f api-service.yaml
   ```

3. **Check to make sure the service is running**:

   ```bash
   kubectl get svc
   ```

4. **Next, run the Kubernetes proxy**:

   ```bash
   kubectl proxy
   ```

Once the service is running, you should be able to access the API service through the NodePort (e.g., `http://<node-ip>:30080`). This will expose your API service outside the cluster, and traffic will be forwarded to the correct pods.

````
