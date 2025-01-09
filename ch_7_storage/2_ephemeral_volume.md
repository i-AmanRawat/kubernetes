# Ephemeral Volumes in Kubernetes

In the previous lesson, we learned that the filesystem in containers is ephemeral, meaning that when a pod is deleted or replaced, all data in the container's filesystem is lost. This is a problem for applications that need to persist data across pod restarts or need to share data between containers in a pod.

Kubernetes provides a **volume abstraction** that helps solve two primary problems:

1. **Data Persistence**: Ensuring that data is preserved beyond the lifecycle of a single pod.
2. **Data Sharing Across Containers**: Allowing containers within the same pod to share data between them.

Some volumes are ephemeral, which means they exist only as long as the pod is running. These are useful when you need to share data between containers in the same pod but don't need to persist it once the pod is deleted.

## Assignment: Scaling the Crawler Service with Shared Volumes

Now let's go back to the **crawler service**. This service is responsible for crawling Project Gutenberg and exposing the data via a JSON API, which is then used in the chat application. Currently, the crawler stores its data in memory, and each instance crawls only one book every 30 seconds.

When scaling up beyond one instance, we need all instances (pods) to share the same data. To achieve this, we will configure a shared **ephemeral volume** to be used by all crawler containers.

### Step 1: Check Crawler Logs

Before making any changes, check the current state of the crawler pod to see its behavior:

```bash
kubectl logs <crawler-podname>
```

You should see logs with timestamps indicating that the crawler is processing books (one every 30 seconds).

### Step 2: Update the Crawler Deployment to Use a Shared Volume

To allow multiple crawler containers to share the same data, we will configure a shared ephemeral volume.

1. **Add a `volumes` section** to the `spec/template/spec` of the crawler's deployment YAML file to create a shared volume.

```yaml
volumes:
  - name: cache-volume
    emptyDir: {}
```

- `emptyDir` creates an ephemeral volume that is initially empty, and its data is stored on the node where the pod is running. The volume is shared between all containers in the pod.

2. **Add a `volumeMounts` section** to the container specification to mount the shared volume to the `/cache` directory.

```yaml
volumeMounts:
  - name: cache-volume
    mountPath: /cache
```

This will mount the shared `cache-volume` at `/cache` in each container.

### Step 3: Scale the Number of Crawler Containers

Next, we will scale up the number of crawler containers in the pod to increase the rate of crawling.

1. **Duplicate the crawler container** definition two more times, giving each container a unique name:

```yaml
containers:
  - name: synergychat-crawler-1
    image: synergychat-crawler:latest
    ports:
      - containerPort: 8080
    volumeMounts:
      - name: cache-volume
        mountPath: /cache
    env:
      - name: CRAWLER_DB_PATH
        value: /cache/db

  - name: synergychat-crawler-2
    image: synergychat-crawler:latest
    ports:
      - containerPort: 8081
    volumeMounts:
      - name: cache-volume
        mountPath: /cache
    env:
      - name: CRAWLER_DB_PATH
        value: /cache/db

  - name: synergychat-crawler-3
    image: synergychat-crawler:latest
    ports:
      - containerPort: 8082
    volumeMounts:
      - name: cache-volume
        mountPath: /cache
    env:
      - name: CRAWLER_DB_PATH
        value: /cache/db
```

Now, we have three containers (`synergychat-crawler-1`, `synergychat-crawler-2`, and `synergychat-crawler-3`), each using the shared `/cache` directory to store their crawled data.

### Step 4: Update the Crawler ConfigMap

We also need to update the `ConfigMap` for the crawler service to provide the path where the crawlers will store their data.

1. Add a new environment variable for the crawler containers in the `ConfigMap`:

```yaml
env:
  - name: CRAWLER_DB_PATH
    value: /cache/db
```

This makes the path `/cache/db` available as an environment variable for each of the crawler containers.

2. Add new ports to the `ConfigMap` to avoid the "address already in use" error:

```yaml
env:
  - name: CRAWLER_PORT_2
    value: "8081"
  - name: CRAWLER_PORT_3
    value: "8082"
```

### Step 5: Fix Port Conflicts

Since all containers in the same pod share the same network namespace, they cannot all bind to the same port. By default, each container will try to bind to port 8080, causing a conflict.

To resolve this, we will bind each container to a different port:

1. Modify the second and third containers to use different ports (`8081` and `8082`) by adding the corresponding environment variable mappings:

```yaml
env:
  - name: CRAWLER_PORT
    valueFrom:
      configMapKeyRef:
        name: crawler-config
        key: CRAWLER_PORT_2
```

Repeat this for the third container, binding it to port `8082`.

### Step 6: Apply the Changes

Once all the changes have been made, apply the updated `ConfigMap` and deployment YAML files:

```bash
kubectl apply -f crawler-configmap.yaml
kubectl apply -f crawler-deployment.yaml
```

### Step 7: Verify the Crawler Pods

Now, check the status of the crawler pods:

```bash
kubectl get pods
```

You should see that all three containers are running, each with its own port but sharing the same volume at `/cache`.

### Step 8: Check Logs for All Containers

Since we now have three containers in the pod, we can check the logs for all of them:

```bash
kubectl logs <pod-name> --all-containers
```

Look for messages such as `listen tcp :8080: bind: address already in use`, which indicates that the second and third containers were bound to different ports.

### Step 9: Start the Kubernetes Proxy

To test everything, start the Kubernetes proxy:

```bash
kubectl proxy
```

Then, you can run HTTP tests using a CLI tool to make sure that the crawler is now able to crawl books concurrently and store the results in the shared volume.

### Summary

In this exercise, we:

1. **Configured shared ephemeral volumes** between multiple crawler containers in a pod using `emptyDir`.
2. **Scaled the number of crawler containers** in the pod to speed up the crawling process.
3. **Avoided port conflicts** by assigning different ports to each crawler container.
4. **Updated the ConfigMap** to include new environment variables for shared storage paths and ports.
5. Finally, we used `kubectl proxy` to expose the service and ran HTTP tests to verify that the crawler works as expected.

In the next step, we'll explore how to configure **persistent storage** for the crawler service to ensure that the data is not lost when the pod is deleted.
