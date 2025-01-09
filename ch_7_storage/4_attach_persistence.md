# Attach Persistence to the API Application

So far, we've created an empty persistent volume. Now, let's configure the API application to use this persistent volume for storing data.

## Assignment

Follow the steps below to update the `api-deployment` to reference the Persistent Volume Claim (PVC) and mount it inside the container.

1. **Create a New Volume in the `api-deployment`**  
   Reference the PVC you've created by adding the following `volumes` configuration in your deployment YAML:

   ```yaml
   volumes:
     - name: synergychat-api-volume
       persistentVolumeClaim:
         claimName: synergychat-api-pvc
   ```

````

2. **Mount the Volume Inside the Container**
   In the same deployment configuration, mount the volume inside the container at the `/persist` directory:

   ```yaml
   volumeMounts:
     - name: synergychat-api-volume
       mountPath: /persist
   ```

3. **Update the Environment Variable**
   Update the environment variable in your application configuration to use the new mount path for the database file (`db.json`):

   ```yaml
   - name: DB_FILE_PATH
     value: "/persist/db.json"
   ```

4. **Apply the Changes**
   After modifying your deployment configuration, apply the changes:

   ```bash
   kubectl apply -f api-deployment.yaml
   ```

5. **Check Pod Status**
   Ensure all your pods are healthy and running with the following command:

   ```bash
   kubectl get pods
   ```

6. **Verify the Application**
   With your tunnel running (`minikube tunnel -c`), open the following URL in your browser:

   ```
   http://synchat.internal/
   ```

   Send some messages to test the application.

7. **Test Persistence**
   To verify that the persistent volume is correctly configured, delete the `api` pod:

   ```bash
   kubectl delete pod <api-pod-name>
   ```

   Once the new pod is running, refresh the page and check if your messages are still available. If the messages persist, it confirms that the persistent volume is functioning correctly.

## Conclusion

By following these steps, you've successfully attached a persistent volume to your API deployment. This ensures that data will persist even when the pods are deleted or restarted.

```
````
