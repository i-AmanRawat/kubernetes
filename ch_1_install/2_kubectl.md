# Kubectl

The Kubernetes command-line tool, **kubectl**, allows you to interact with your Kubernetes cluster. It communicates directly with the Kubernetes API server to manage cluster resources like pods, deployments, and services.

## Install Kubectl

To get started with **kubectl**, follow the [official installation instructions for kubectl](https://kubernetes.io/docs/tasks/tools/).

### Installation Steps:

1. **Windows**: You can install **kubectl** using [chocolatey](https://chocolatey.org/packages/kubernetes-cli) or [curl](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/).
2. **macOS**: Use [Homebrew](https://brew.sh/) to install kubectl:
   ```bash
   brew install kubectl
   ```
3. **Linux**: Download the latest stable release of kubectl and move it to a directory in your system's PATH. You can follow the steps [here](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/).

## Verify Installation

After installation, run the following command to verify that **kubectl** is correctly installed:

```bash
kubectl version
```

### Expected Output:

You will see the client version (your local version of kubectl), but there won't be a server version yet since we haven't connected to a Kubernetes cluster.

Example output:

```bash
Client Version: version.Info{Major:"1", Minor:"21", GitVersion:"v1.21.0", GitCommit:"1234567", GitTreeState:"clean", BuildDate:"2021-05-18T20:28:00Z", GoVersion:"go1.16.5", Compiler:"gc", Platform:"linux/amd64"}
```

## Create a Git Repository for This Course

Throughout the course, you'll be creating many configuration files for Kubernetes. To keep track of these, it's best to use version control (e.g., **Git**).

1. Go to [GitHub](https://github.com/) and create a new repository for this course, naming it something like `kubernetes-course`.
2. Clone the repository to your local machine:

   ```bash
   git clone https://github.com/your-username/kubernetes-course.git
   ```

3. After cloning the repository, navigate to it:

   ```bash
   cd kubernetes-course
   ```

4. For the rest of this course, store your Kubernetes configuration files inside this repo.

By the end of the course, you will have a collection of Kubernetes YAML files and other configuration files in your GitHub repository that you can reuse in real-world projects.

> **Tip**: Make sure to commit your changes regularly so you can easily track your progress!

```

```
