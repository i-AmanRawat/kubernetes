# Deployments

A **Deployment** provides declarative updates for Pods and ReplicaSets.  
You describe your desired state in a Deployment, and the Deployment Controller's job is to make the current state match the desired state. You declare your hopes and dreams, and it's Kubernetes' job to make them come true.

## Why Deleting a Pod Doesn't Feel Like a Deletion?

Remember when we had you delete a pod, only to see that a new pod was created in its place? It's kinda like chopping heads off of a hydra.

That's because the desired state described in our Deployment says we want 2 pods running at all times. When we delete one, the Deployment Controller sees that the current state doesn't match the desired state, so it creates a new pod to make them match again.

## Assignment

1. Take a look at the YAML file for your current deployment in the CLI:

   ```bash
   kubectl get deployment synergychat-web -o yaml
   ```

2. Edit the deployment and change the number of replicas from 2 to 10:

   ```bash
   kubectl edit deployment synergychat-web
   ```

3. Make sure you've got 10 pods running:

   ```bash
   kubectl get pod
   ```

4. Keep using `kubectl get pod` to check on your pods until all 10 are in a "ready" state. Once they are, run:

   ```bash
   kubectl proxy
   ```
