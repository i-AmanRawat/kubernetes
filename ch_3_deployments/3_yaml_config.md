# YAML Config

Kubernetes resources are primarily configured using **YAML** files. We've used the `kubectl edit` command to edit resources in the cluster on-demand, but let's inspect our deployment's YAML file a bit more closely.

Note: A hyphen is how you denote a list item in yaml

## Assignment

1. cmd to download a copy of your deployment's YAML file:

   ```bash
   kubectl get deployment synergychat-web -o yaml > web-deployment.yaml
   ```

2. Then open it in your text editor. There are 5 top-level fields in the file:

   - **apiVersion**: `apps/v1` - Specifies the version of the Kubernetes API you're using to create the object (e.g., `apps/v1` for Deployments).
   - **kind**: `Deployment` - Specifies the type of object you're configuring.
   - **metadata**: Metadata about the deployment, like when it was created, its name, and its ID.
   - **spec**: The desired state of the deployment. Most impactful edits, like how many replicas you want, will be made here.
   - **status**: The current state of the deployment. You won't edit this directly; it's just for you to see what's going on with your deployment.

3. Inside your editor, change the number of replicas to `3` and save the file.  
   **Note:** You're just editing a file on your machine! It won't yet have any effect on the deployment in your cluster.

4. To apply the changes, run:

   ```bash
   kubectl apply -f web-deployment.yaml
   ```

   You should get a warning that lets you know you're missing the `last-applied-configuration` annotation.  
   That's okay! You got that warning because we created this deployment the quick and dirty way by using `kubectl create deployment` instead of creating a YAML file and using `kubectl apply -f`.

   However, because we've now updated it with `kubectl apply`, the annotation is now there, and we won't get the warning again.

5. Download the YAML file again and take a look at it. You should see the annotation now.

6. Apply the configuration a second time. You won't get the warning.

7. **Save this YAML file in a Git repo for this course!** We'll be making more configuration files. Kubernetes is an "infra-as-code" tool, so it's important to keep your configuration files in a Git repo.

8. Finally, start the proxy server:

   ```bash
   kubectl proxy
   ```
