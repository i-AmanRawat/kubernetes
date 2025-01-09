# Persistence in Kubernetes

All the volumes we've worked with so far have been **ephemeral**, meaning when the associated pod is deleted, the volume is deleted as well. This is fine for some use cases, but for most **CRUD** (Create, Read, Update, Delete) apps, we want to persist data even if the pod is deleted.

### Why is Persistence Important?

It's not just when pods are explicitly deleted with `kubectl` that we need to worry about data loss. Pods can be deleted for several reasons:

- The node they're running on could fail
- A new version of the image was published (e.g., code was updated)
- A new node was added to the cluster, and the pod was rescheduled

In all of these cases, we want to make sure that our data is still available. This is where **Persistent Volumes (PVs)** come into play.

## Persistent Volumes (PV)

Instead of simply adding a volume to a deployment, a **Persistent Volume** is a **cluster-level resource** that is created separately from the pod and then attached to the pod. It's similar to a **ConfigMap** in that way.

### Types of Persistent Volumes

PVs can be created in two ways:

- **Static PVs**: Created manually by a cluster administrator.
- **Dynamic PVs**: Created automatically when a pod requests a volume that doesn't exist yet.

Generally speaking, especially in the **cloud-native world**, we prefer using **dynamic PVs** because they are easier to manage and provide more flexibility.

## Persistent Volume Claims (PVC)

A **Persistent Volume Claim (PVC)** is a request for a persistent volume. When using dynamic provisioning, a PVC will automatically create a PV if one doesn't exist that matches the claim.

The PVC is then attached to a pod, just like a regular volume would be.

### PVC and PV Workflow

1. **Create a PVC**: This is a request for storage resources.
2. **PV is created (or matched)**: A persistent volume is either manually created or dynamically provisioned based on the PVC.
3. **Volume is attached to the pod**: Once the PVC is bound to a PV, it is mounted to the pod for use.

## Assignment: Creating and Managing PVC

### Step 1: Create a PVC Definition

Create a new file called `api-pvc.yaml` and add the following content:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: xxx
spec:
  accessModes:
    - xxx
  resources:
    requests:
      storage: xxx
```

````

### Step 2: Modify the PVC Definition

Add the following values to the YAML file to define your Persistent Volume Claim:

```yaml
metadata/name: synergychat-api-pvc
spec/accessModes:
  - ReadWriteOnce
spec/resources/requests/storage: 1Gi
```

This creates a new PVC called **synergychat-api-pvc** with the following properties:

- **accessModes**: Allows read/write access by only one node at a time (`ReadWriteOnce`).
- **resources/requests/storage**: Requests 1 GB of storage.

### Step 3: Apply the PVC

Run the following command to apply your PVC:

```bash
kubectl apply -f api-pvc.yaml
```

### Step 4: Check the PVC and PV

Run these commands to verify that the PVC and PV were created successfully:

```bash
kubectl get pvc
kubectl get pv
```

You should see that a new PV was created automatically in response to the PVC!

### Step 5: Delete the PVC

Delete the PVC using the following command:

```bash
kubectl delete pvc <pvc-name>
```

Replace `<pvc-name>` with the actual name of your PVC (e.g., `synergychat-api-pvc`).

### Step 6: Verify Deletion

Run the following commands to ensure both the PVC and PV have been deleted:

```bash
kubectl get pvc
kubectl get pv
```

### Step 7: Recreate the PVC

Now, recreate the PVC. This demonstrates the power of **dynamic provisioning**. To restart the dynamic provisioning process, run:

```bash
kubectl proxy
```

Then, run and submit HTTP tests using a CLI tool to verify the PVC is being dynamically provisioned again.

## Conclusion

Persistent Volumes (PVs) and Persistent Volume Claims (PVCs) are essential components in Kubernetes for managing data persistence. Dynamic provisioning of PVs based on PVCs simplifies managing storage and ensures data is available even if pods or nodes fail.

```
````
