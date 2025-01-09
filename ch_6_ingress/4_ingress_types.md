# Kubernetes Ingress Types and Annotations

## API Versioning for Ingress

You may have noticed that at the top of all our resources we have the following in the YAML:

```yaml
apiVersion: v1
```

This is the **API version** of the resource, and because these resources (like **Pods**, **Services**, and **Deployments**) are core to Kubernetes, they fall under the standard **v1 API group**.

However, **Ingress** is not a core Kubernetes resource. It’s considered an extension resource. That’s why it uses:

```yaml
apiVersion: networking.k8s.io/v1
```

### Key Points About Ingress API:

- **networking.k8s.io** is an API group designed for network-related objects (like Ingress, NetworkPolicy, etc.).
- **Ingress** provides HTTP and HTTPS routing to services, so it’s not part of the core API but a **core extension**.
- This distinction is important because **Ingress** and other extended resources follow a slightly different lifecycle and support specific features related to traffic routing, SSL termination, and load balancing, among others.

## What Are Annotations in Kubernetes?

You might have noticed that we used a single **annotation** on our Ingress resource:

```yaml
annotations:
  nginx.ingress.kubernetes.io/rewrite-target: /
```

### What is an Annotation?

In Kubernetes, **annotations** are **key-value pairs** used to attach arbitrary metadata to resources. They provide a way to **extend Kubernetes’ functionality** without modifying the resource itself. Unlike labels, which are typically used for identification or grouping, annotations are intended for storing **non-identifying data**, like configuration settings or additional instructions for tools interacting with Kubernetes.

### Common Annotation Patterns in Kubernetes

The Kubernetes API is intentionally kept small and simple, but there are many use cases that need more complex functionality. Instead of adding these features directly to the Kubernetes API, the community has introduced **annotations** for various extensions and controllers to act upon.

For example, in our case, the **nginx.ingress.kubernetes.io/rewrite-target** annotation tells the **Ingress controller** (in this case, NGINX) how to rewrite incoming URLs before forwarding them to backend services:

```yaml
annotations:
  nginx.ingress.kubernetes.io/rewrite-target: /
```

- This configuration is helpful when we want to make the incoming URL path match the backend service’s expected path structure.
- In the case of a web application, we might want to strip off the `/synchat` prefix so that the path goes directly to the root of the service.

## Cloud-Specific Ingress Annotations

In many Kubernetes deployments, particularly in cloud environments, you'll encounter **cloud-specific annotations**. These annotations are used to configure **Ingress controllers** in cloud environments like **Google Cloud**, **AWS**, or **Azure**.

Here is an example from **Google Cloud**:

```yaml
annotations:
  kubernetes.io/ingress.global-static-ip-name: static-ip-name-here
  networking.gke.io/managed-certificates: cert-name-here
  kubernetes.io/ingress.class: gce
```

### Explanation of the Above Annotations:

1. **`kubernetes.io/ingress.global-static-ip-name`**:

   - Specifies the name of the **static IP** that should be used for the Ingress.
   - Useful for situations where you want to reserve a static IP for your application, ensuring it remains consistent even if the Ingress or services change.

2. **`networking.gke.io/managed-certificates`**:

   - Tells GKE (Google Kubernetes Engine) to use a specific **managed SSL certificate**.
   - This is crucial for HTTPS traffic and SSL termination at the ingress controller.

3. **`kubernetes.io/ingress.class: gce`**:
   - This specifies that the **GCE Ingress controller** (Google Cloud Engine) should be used to manage the Ingress resource.
   - Each cloud provider or third-party tool might have its own controller, and this annotation helps Kubernetes determine which controller should process the resource.

### Cloud Provider-Specific Annotations

Each major cloud provider has its own set of annotations and controllers for configuring **Ingress**:

- **AWS**: AWS may use annotations to define the type of load balancer or SSL certificates.
- **GCP**: As shown above, Google Cloud uses annotations like **global-static-ip-name** and **managed-certificates** for specific configurations.
- **Azure**: Azure also provides annotations to configure ingress for internal or external load balancing.

If you're working in a **cloud-native** Kubernetes environment (such as **GKE**, **EKS**, or **AKS**), you will need to use annotations specific to that cloud platform to make full use of the cloud-native features for load balancing, IP address management, and SSL/TLS.

### Kubernetes Ingress Extensions

Ingress controllers (like **NGINX**, **Traefik**, and **HAProxy**) can also use annotations to provide additional features and custom behavior for routing traffic. These controllers often include specific configuration options that let you tweak how traffic is managed, routed, and balanced.

---

## Conclusion: Using Annotations in Kubernetes Ingress

Annotations are a key part of configuring Kubernetes resources like **Ingress**. They provide flexibility by allowing Kubernetes to integrate with external tools and platforms, especially for more advanced use cases like:

- Routing traffic to services based on the URL path or host.
- Managing SSL/TLS certificates and load balancers.
- Integrating with cloud services for IP management, traffic handling, etc.

As you move into production, particularly in cloud environments, **annotations** will be essential for customizing how your **Ingress controllers** behave and interact with other cloud-native tools.

---

## Next Steps:

- **Check your cloud provider's documentation** for specific annotations related to Ingress controllers.
- Experiment with cloud-specific annotations for SSL/TLS termination, static IPs, and routing.
- Learn how to configure **NGINX Ingress** or **Traefik** Ingress controllers and use annotations to adjust settings based on your needs.

```

### Additions/Clarifications:
- **Explanation of Annotations**: I clarified the role of annotations in Kubernetes, explaining how they differ from labels and their purpose.
- **Cloud-Specific Example**: I expanded on the Google Cloud-specific annotations example, explaining each annotation's purpose more clearly.
- **Context on Kubernetes Extensions**: I added context about **Ingress controllers** and how they work with annotations to manage cloud-specific behavior (like SSL or IP management).

```
