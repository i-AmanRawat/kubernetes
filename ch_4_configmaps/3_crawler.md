# Crawler

We've got one last application to deploy to our cluster: the **crawler**. This is an application that continuously crawls Project Gutenberg and exposes the juicy data it finds via a **JSON API**.

## Assignment

### 1. Add a New Config Map

Create a copy of your `api-configmap.yaml` file and call it `crawler-configmap.yaml`. We're going to make a few changes to it.

- Name it `synergychat-crawler-configmap` instead of `synergychat-api-configmap`.
- Remove the `API_PORT` environment variable.
- Add some new environment variables:

  - `CRAWLER_PORT: "8080"`
  - `CRAWLER_KEYWORDS: love,hate,joy,sadness,anger,disgust,fear,surprise`

  **Note:** It's okay that the `CRAWLER_PORT` in the crawler deployment is the same as the `API_PORT` in the API deployment. They're in different pods, and these are pod-internal ports.

Here is a reference to the docs for the **SynergyChat microservices on GitHub** in case you want additional info about the crawler.

Deploy the config map:

```bash
kubectl apply -f crawler-configmap.yaml
```

````

### 2. Add a New Deployment

Create a copy of your `api-deployment.yaml` file and call it `crawler-deployment.yaml`. We're going to make a few changes to it:

- **Update all `synergychat-api` references** to `synergychat-crawler`.
- **Update the image URL** to `bootdotdev/synergychat-crawler:latest`.
- **Update the environment variable references** to match the new config map.

For the API service, we used this syntax to connect the config map to the deployment:

```yaml
spec:
  containers:
    - image: bootdotdev/synergychat-api:latest
      name: synergychat-api
      env:
        - name: API_PORT
          valueFrom:
            configMapKeyRef:
              name: synergychat-api-configmap
              key: API_PORT
```

If we use this same format for the crawler, it gets kinda verbose and repetitive to list out each environment variable:

```yaml
spec:
  containers:
    - image: bootdotdev/synergychat-api:latest
      name: synergychat-api
      env:
        - name: THING_ONE
          valueFrom:
            configMapKeyRef:
              name: synergychat-api-configmap
              key: THING_ONE
        - name: THING_TWO
          valueFrom:
            configMapKeyRef:
              name: synergychat-api-configmap
              key: THING_TWO
        - name: THING_THREE
          ...
```

Instead, we can use the `envFrom` key to reference the entire config map and make it available to the pods in the deployment:

```yaml
envFrom:
  - configMapRef:
      name: synergychat-crawler-configmap
```

Once you've updated the deployment, apply it:

```bash
kubectl apply -f crawler-deployment.yaml
```

If the pod isn't "ready", check the logs to see if there's an error. If the error is related to environment variables, debug your config map and deployment files and reapply them.

### 3. Forward the Pod's Port

Once the pod is ready, forward the pod's `8080` port to your local machine:

```bash
kubectl port-forward <pod-name> 8080:8080
```
````
