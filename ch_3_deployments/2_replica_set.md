# Replica Sets

A **ReplicaSet** maintains a stable set of replica Pods running at any given time.  
It's the thing that makes sure that the number of Pods you want running is the same as the number of Pods that are actually running.

You might be thinking, "I thought that's what a Deployment does." Well... yes.

A **Deployment** is a higher-level abstraction that manages the ReplicaSets for you. You can think of a Deployment as a wrapper around a ReplicaSet. Here's the rub:

You will probably never use ReplicaSets directly. I just need to mention what they are because you'll hear the term thrown around, and might even see them referenced in logs and such.

## Look at Your Replica Sets

Let's take a look at the ReplicaSets that are running in your cluster:

```bash
kubectl get replicasets
```

Just like with pods, notice that we never directly created the ReplicaSet. We created a deployment, and the deployment created the ReplicaSet.
