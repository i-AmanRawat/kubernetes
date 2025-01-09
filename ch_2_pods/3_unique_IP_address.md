# Unique IP Addresses in Kubernetes Pods

In Kubernetes, every Pod is assigned a **unique IP address** within the cluster. This unique IP allows for simple communication and service discovery between Pods. Whether Pods are on the same Node or distributed across different Nodes, they can communicate easily using their assigned IP addresses.

### Key Points about Pod IPs:

- **Unique IPs**: Each Pod gets its own internal IP address, which is separate from the Node's IP address. This makes it easier to manage and scale applications.
- **Virtualized Network**: The IP address of a Pod is virtual and can only be accessed from within the cluster. Pods do not directly expose their IP addresses to the outside world.
- **Service Discovery**: Kubernetes handles communication between Pods, even if they are running on different Nodes, using these unique IPs. This is part of how Kubernetes abstracts the network layer and simplifies the application architecture.

## Assignment: Exploring Pod IPs

Now, let's look at how to view the unique IPs of Pods and interact with your cluster via the `kubectl` proxy.

### Steps:

1. **Get a "wide" output of your Pods**:

   To see additional information about your Pods, including their IP addresses, run the following command:

   ```bash
   kubectl get pods -o wide
   ```

   This command will provide a wide output, including columns such as **IP** that show each Pod's unique IP address. You should notice that each Pod has its own unique IP address.

2. **Start the `kubectl` proxy**:

   The `kubectl proxy` command starts a proxy server on your local machine, which allows you to interact with the Kubernetes API from your browser. Run:

   ```bash
   kubectl proxy
   ```

   By default, this will start the proxy on `http://127.0.0.1:8001`.

3. **Access the Pods via the proxy**:

   Once the proxy is running, navigate to the following URL in your browser:

   ```
   http://127.0.0.1:8001/api/v1/namespaces/default/pods
   ```

   You should see a large JSON response that provides details about the Pods running in the **default** namespace of your cluster.

4. **Run and Submit the HTTP Tests**:

   Use the CLI tool to run the HTTP tests. These tests will verify that:

   - The proxy server is accessible.
   - The proxy is returning the correct information about your Pods in the cluster.

---

### Key Takeaways:

- **Unique IPs** are assigned to each Pod in Kubernetes, making internal communication and service discovery straightforward.
- The **Pod IP** is **virtual** and can only be accessed from within the cluster.
- By using `kubectl proxy`, you can easily interact with your Kubernetes cluster via a local proxy and inspect the API's response.

```

### Key Concepts:
- **Pod IP**: Each Pod in Kubernetes is assigned a unique IP address, which is accessible only within the cluster.
- **kubectl proxy**: Allows you to interact with your Kubernetes cluster through a local proxy running on your machine.
- **Service Discovery**: Kubernetes handles internal communication between Pods using their unique IPs, making it easier to scale and manage applications.
```
