# Intra-Cluster DNS in Kubernetes

In Kubernetes, services within the same cluster can communicate with each other through **intra-cluster DNS**. This enables direct HTTP requests between services without the need for external ingress or domain names.

### Current Communication Setup

The front-end of SynergyChat communicates with the API application through an external ingress:

1. **Domain**: `http://synchatapi.internal`
2. **Flow**: ingress → service → pod

However, now we need to connect the **crawler** and **API** applications for internal communication.

### Internal Communication Between API and Crawler

The **API** needs to make HTTP requests directly to the **crawler** to fetch the latest data for the `/stats` slash command.

1. **Flow**: front-end → API → crawler
2. The communication between the API and crawler is internal, so there's no need for an external domain name or ingress. This makes the setup simpler, faster, and more secure.

### Testing the Slash Command

With the tunnel running (`minikube tunnel -c`), open the following URL in your browser:

```
http://synchat.internal/
```

- Post a new message with `/stats` as the message text.
- You should see a response like this:

```
crawler-bot: Crawler worker not configured
```

This is because the API application doesn't yet know how to communicate with the crawler. Let's resolve that by configuring the API to use Kubernetes' internal DNS to reach the crawler.

## Using Kubernetes DNS

Kubernetes automatically creates DNS entries for each service in the cluster. The format for internal DNS resolution is:

```
<service-name>.<namespace>.svc.cluster.local
```

For example, if the service is called `crawler` and it resides in the `default` namespace, the DNS name would be:

```
crawler.default.svc.cluster.local
```

## Assignment: Connecting API to Crawler

### Steps to Update the API Configuration

1. **Update the API ConfigMap and Deployment**  
   Add a new environment variable called `CRAWLER_BASE_URL` to the API application's configuration. Its value should be the DNS entry for the crawler service:

   ```yaml
   - name: CRAWLER_BASE_URL
     value: "http://<service-name>.<namespace>.svc.cluster.local"
   ```

   - Replace `<service-name>` with the name of the crawler service (e.g., `crawler`).
   - Replace `<namespace>` with the namespace where the crawler service is located (e.g., `default`).

2. **Apply the Changes**  
   After updating the ConfigMap and Deployment, apply the changes to the API application:

   ```bash
   kubectl apply -f <api-configmap-file>.yaml
   kubectl apply -f <api-deployment-file>.yaml
   ```

3. **Verify the Connection**  
   Once the changes are applied, go back to SynergyChat and post a new message with `/stats` as the message text.

   This time, the API should be able to communicate with the crawler, and you should see a response.

### Troubleshooting

- **If it's not connecting**: You may need to include the port number in the URL. For example:

  ```yaml
  - name: CRAWLER_BASE_URL
    value: "http://<service-name>.<namespace>.svc.cluster.local:8080"
  ```

- Replace `8080` with the appropriate port number if necessary.

## Conclusion

By using Kubernetes' built-in DNS system, services like the API and crawler can communicate directly with each other within the cluster. This setup improves performance, security, and ease of management by removing the need for external DNS or ingress configurations for internal communication.

```

### What Are port and targetPort?
port: The port exposed by the Kubernetes Service. This is what clients (e.g., your API application) use to communicate with the service.
targetPort: The port on the pods where the service forwards traffic. This is where your application container is listening for incoming traffic.

```
