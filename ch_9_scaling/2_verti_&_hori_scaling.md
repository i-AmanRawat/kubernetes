# Vertical and Horizontal Scaling in Kubernetes

Generally speaking, there are two ways to scale an application: **vertically** and **horizontally**. When I say "scaling", I'm referring to increasing the capacity of an application. Let's explore both types of scaling.

## Vertical Scaling

Vertical scaling involves increasing the resources available to a single instance of the application. For example, consider a web server that needs to handle roughly **1000 requests per second**. To support this, the server might use about:

- **1/2 of a CPU core**
- **1 GB of RAM**

If we want to scale up to handle **2000 requests per second**, we could double the CPU and RAM, like so:

- **1 CPU core**
- **2 GB of RAM**

This type of scaling is called **vertical scaling** because we're increasing the capacity of the application by adding more resources (CPU, RAM) to the same instance of the application.

**Limitations of Vertical Scaling:**

- Vertical scaling works well until the hardware limitations kick in.
- You can only scale up as much as your hardware allows (i.e., the maximum number of CPUs and the amount of RAM your node can support).
- Eventually, vertical scaling reaches a point where it’s no longer practical or efficient.

## Horizontal Scaling

Horizontal scaling, on the other hand, involves increasing the **number of instances** of the application (in Kubernetes, this means creating more pods). Instead of increasing the resources of a single application instance, we distribute the load across multiple instances.

For example, to handle more requests, instead of adding more CPU or RAM to a single server, you add more servers (or pods). These pods can be distributed across different nodes, allowing for more flexibility and scalability.

**Benefits of Horizontal Scaling:**

- Horizontal scaling can continue as long as you have more nodes to run additional pods.
- It’s often more efficient in systems like Kubernetes because the application can distribute the load and handle failover more gracefully.

## Conclusion: Which is Better?

When working in systems like **Kubernetes**, it's generally better to scale **horizontally** rather than vertically. Horizontal scaling provides more flexibility and helps you make better use of available resources by distributing the load across multiple instances. This is one of the main reasons Kubernetes favors scaling out (horizontally) over scaling up (vertically).

```

```
