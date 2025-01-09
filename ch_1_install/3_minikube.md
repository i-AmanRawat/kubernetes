# Minikube

Minikube is a tool that allows you to run a **single-node Kubernetes cluster** on your local machine, which is ideal for testing and development. In a production environment, you would likely use a multi-node cluster, often in the cloud. However, Minikube simplifies running Kubernetes locally, making it a great tool for practice and learning.

## Install Minikube

To get started, follow the official installation instructions for Minikube from the official documentation:  
[Install Minikube](https://minikube.sigs.k8s.io/docs/)

### Requirements:

Before installing, make sure your system meets the requirements listed on the official page. Minikube requires a **hypervisor** (e.g., VirtualBox, HyperKit, KVM) to run, and some systems may need additional steps to enable virtualization.

### Installation Steps:

1. **Windows**: You can use [Chocolatey](https://chocolatey.org/packages/minikube) or [Download the Minikube Installer](https://github.com/kubernetes/minikube/releases).
2. **macOS**: Use [Homebrew](https://brew.sh/):

   ```bash
   brew install minikube
   ```

3. **Linux**: Download the latest Minikube release and follow the installation instructions for your distribution.  
   More details [here](https://minikube.sigs.k8s.io/docs/).

## Verify Minikube Installation

After installation, verify that Minikube is correctly installed by running:

```bash
minikube version
```

This command should display the version of Minikube you installed.

### Expected Output:

```bash
minikube version: v1.24.0
```

## Start Minikube

Before starting Minikube, ensure that **Docker** is running on your system. Kubernetes will be using Docker to manage containers.

1. **Start Minikube**: To start the Kubernetes cluster on your local machine, run:

   ```bash
   minikube start --extra-config "apiserver.cors-allowed-origins=[\"http://boot.dev\"]"
   ```

   - This will create the cluster and configure the Kubernetes API to allow connections from the **Boot.dev** environment.
   - The process may take a few minutes, and once complete, you should see a message like:
     ```
     kubectl is now configured to use "minikube" cluster and "default" namespace by default
     ```

## Access the Minikube Dashboard

Minikube includes a local dashboard that you can use to view and manage your Kubernetes cluster.

1. To start the dashboard, run:

   ```bash
   minikube dashboard --port=63840
   ```

   - This will open the dashboard in your browser.
   - The port `63840` is specified to align with the **Boot.dev** CLI tool's port configuration.

   You can use this dashboard to inspect and manage your Kubernetes resources, but we won’t use it much during the course.

## Keep Minikube Running

Make sure that Minikube is **running** throughout the course, as we’ll be interacting with Kubernetes on your local cluster using the `kubectl` CLI tool.

## Troubleshooting

### Virtualization Issues (WSL on Windows)

If you are using **Windows Subsystem for Linux (WSL)**, make sure virtualization is enabled.

1. Go to **Docker Desktop Settings**:
   - Navigate to **Resources → WSL Integration**.
   - Ensure **Integration with the default WSL distro** is enabled.

### Conflicts with Previous Minikube Installations

If you have run into issues from previous Minikube installations, you can clean them up by running:

```bash
minikube stop
minikube delete
```

After stopping and deleting any existing clusters, restart Minikube:

```bash
minikube start
```

## Run and Submit the HTTP Tests

Once Minikube is running, you can use the **Boot.dev CLI tool** to test your setup. Run the HTTP tests and submit them to make sure your environment is correctly configured.

---

By following these steps, you should have a fully functional local Kubernetes cluster with Minikube, ready for the rest of the course!

```

```
