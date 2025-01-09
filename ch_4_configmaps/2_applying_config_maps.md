# Applying the Config Map

Now that we have a config map, we need to connect it to our deployment.

## Assignment

1. **Open up your `api-deployment.yaml` file**. We're going to add a few things to it. Under the `containers` section, add the following to the first (and only) entry:

   ```yaml
   env:
     - name: API_PORT
       valueFrom:
         configMapKeyRef:
           name: synergychat-api-configmap
           key: API_PORT
   ```

````

This tells Kubernetes to set the `API_PORT` environment variable to the value of the `API_PORT` key in the `synergychat-api-configmap` config map. Reference the official docs if you're confused about the structure of the YAML.

2. **Next, apply the deployment**. Hopefully, you remember the command for this by now:

   ```bash
   kubectl apply -f api-deployment.yaml
   ```

   Once it's applied, you should be able to take a look at the pods and see that a new API pod has been deployed and **isn't crashing**!

3. **Let's forward the API pod's 8080 port to our local machine** so we can test it out:

   ```bash
   kubectl port-forward <pod-name> 8080:8080
   ```

4. **Test the API**. Make sure it returns a `404` response when you hit the root:

   ```bash
   curl http://localhost:8080
   ```

   You should receive a `404` response, which indicates that the API is up and running, but no resource has been created at the root endpoint yet.

```

# Config Maps are Insecure

ConfigMaps are a great way to manage innocent environment variables in Kubernetes. Things like:

- Ports
- URLs of other services
- Feature flags
- Settings that change between environments, like DEBUG mode

However, they are **not cryptographically secure**. ConfigMaps aren't encrypted, and they can be accessed by anyone with access to the cluster.

If you need to store sensitive information, you should use **Kubernetes Secrets** or a third-party solution. We'll talk more about storing sensitive information in a later chapter.

````
