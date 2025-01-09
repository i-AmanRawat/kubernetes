# DNS Configuration for Local Ingress

Now that we've configured the **Ingress** to route the domains:

- `synchat.internal` to the `web-service`
- `synchatapi.internal` to the `api-service`

We need to configure our local machine to resolve those domains to the **Ingress load balancer**. We won't be setting up global DNS so that anyone on the internet can access our app! We'll just be configuring our local machine to resolve those domains to the Ingress load balancer.

## Assignment

### Step 1: Modify `/etc/hosts` File

On your local machine, there's a file called `/etc/hosts` that is used to resolve domain names to IP addresses. We can add entries to this file to resolve our domains to the **Ingress load balancer**.

#### Steps for Linux and macOS:

1. Open the `/etc/hosts` file in your favorite text editor:

   ```bash
   sudo nano /etc/hosts
   ```

2. Add the following lines at the end of the file:

   ```plaintext
   127.0.0.1        synchat.internal
   127.0.0.1        synchatapi.internal
   ```

   You'll probably need to provide your password to save the file. These entries will resolve the domains `synchat.internal` and `synchatapi.internal` to `127.0.0.1` (which is the localhost IP address).

3. Save the file and exit the editor.

#### Steps for WSL (Windows Subsystem for Linux):

1. For **WSL users**, you'll also need to add the entries above to the Windows `hosts` file. The file is located at: `C:\Windows\System32\drivers\etc\hosts`.
2. The WSL path to this file is: `/mnt/c/Windows/System32/drivers/etc/hosts`. Open the file in a text editor with administrative privileges (run the terminal as Administrator).

   - You can use `nano` or any other text editor to modify the Windows `hosts` file.

### Step 2: Check the Configuration

Now, to ensure that the domains are resolving correctly, run the following command:

```bash
ping synchat.internal
```

````

You should see something like this:

```plaintext
PING synchat.internal (127.0.0.1): 56 data bytes
64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.104 ms
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.105 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.150 ms
...
```

If the domains are resolving correctly, you’ll get replies with `127.0.0.1`.

To stop the ping, press `Ctrl + C`.

### Step 3: Troubleshooting

If you get a **"cannot resolve host"** error, then something went wrong. Double-check that the entries were added correctly to your `/etc/hosts` file.

Also, ensure that you test the **synchatapi.internal** domain as well.

### For Minikube Users

If you're using **Minikube**, the IP address used by Minikube might not be `127.0.0.1`. In that case, find the local IP address for Minikube by running:

```bash
minikube ip
```

Then, replace `127.0.0.1` with the IP address provided by Minikube in your `/etc/hosts` file.

This ensures that your local machine resolves the domains correctly and routes traffic to your Ingress load balancer.

```

This Markdown file contains all the necessary steps and details for modifying the `/etc/hosts` file, setting up DNS locally, and troubleshooting. Let me know if you need further adjustments!
```
````
