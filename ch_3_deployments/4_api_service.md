# API Service

We've deployed one service, and we've deployed multiple instances of it. Time to deploy a second service!

This service doesn't serve a webpage! It's a **JSON API**. It's the backend for our chat application. By deploying the API and configuring the front-end to talk to it, we'll have a functional chat application!

## Create a Deployment Configuration

Let's write a deployment from scratch.

Feel free to reference the Kubernetes docs for examples of the proper structure.

1. **Create a new file** called `api-deployment.yaml`.

2. **Add the `apiVersion` and `kind` fields**:

   - The `apiVersion` is `apps/v1` and, since this is a deployment, the `kind` is `Deployment`.

3. **Add a `metadata/name` field**, and name our deployment `synergychat-api` for consistency.

4. **Add a `metadata/labels/app` field**, and set it to `synergychat-api`. This will be used to select the pods that this deployment manages.

5. **Add a `spec/replicas` field** and set it to `1`. We can always scale up to more pods later.

6. **Add a `spec/selector/matchLabels/app` field** and set it to `synergychat-api`. This should match the label we set in step 4.

7. **Add a `spec/template/metadata/labels/app` field** and set it to `synergychat-api`. Again, this should match the label we set in step 4. Labels are important because they help Kubernetes know which pods belong to which deployments.

8. **Add a `spec/template/spec/containers` field**. This contains a list of containers that will be deployed.
   - **Note**: A hyphen is how you denote a list item in YAML.
   - Set the name of the container to `synergychat-api`.
   - Set the image to `bootdotdev/synergychat-api:latest`. This tells Kubernetes where to download the Docker image from.

Here is the equivalent JSON for reference (you'll need to type it out in YAML format):

```json
{
  "apiVersion": "apps/v1",
  "kind": "Deployment",
  "metadata": {
    "name": "synergychat-api",
    "labels": {
      "app": "synergychat-api"
    }
  },
  "spec": {
    "replicas": 1,
    "selector": {
      "matchLabels": {
        "app": "synergychat-api"
      }
    },
    "template": {
      "metadata": {
        "labels": {
          "app": "synergychat-api"
        }
      },
      "spec": {
        "containers": [
          {
            "name": "synergychat-api",
            "image": "bootdotdev/synergychat-api:latest"
          }
        ]
      }
    }
  }
}
```

````

## Create the Deployment

Once you've created your `api-deployment.yaml` file, apply the deployment:

```bash
kubectl apply -f api-deployment.yaml
```

Next, take a look at all the pods you have running now. You should see pods for the web service and a pod for the API service.

However, you might notice that the API pod isn't in a "ready" state. In fact, it should be stuck in a `CrashLoopBackOff` status. Oh no! We've created a thrashing pod!

## Run the Proxy

To troubleshoot or interact with the cluster, run:

```bash
kubectl proxy
```
````
