# INSTRUCTION.md

## How to Deploy and Validate the Django ToDo App on Kubernetes

### 1. Prerequisites

- Docker installed
- Kubernetes cluster running (e.g., using [kind](https://kind.sigs.k8s.io/))
- `kubectl` configured to access your cluster

---

### 2. Deploy Resources

Run the bootstrap script to create all required resources:

```bash
bash bootstrap.sh
```

---

### 3. Validate PersistentVolume and PersistentVolumeClaim

Check that the PV and PVC are bound:

```bash
kubectl get pv
kubectl get pvc -n todoapp
```

Both should show `STATUS: Bound`.

---

### 4. Validate Deployment and Pod

Check that the deployment and pod are running:

```bash
kubectl get deployments -n todoapp
kubectl get pods -n todoapp
```

Pod status should be `Running`.

---

### 5. Validate Volume Mounts

Exec into the running pod and check the mounted directories:

```bash
kubectl exec -it <pod-name> -n todoapp -- /bin/sh
ls /app/data
ls /app/configs
ls /app/secrets
```

You should see files or directories as expected.

---

### 6. Validate Application

Access the app via NodePort or ClusterIP as configured. For NodePort:

```bash
kubectl get svc -n todoapp
```

Open `http://localhost:<NodePort>` in your browser.

---

### 7. Clean Up

To remove all resources:

```bash
kubectl delete namespace todoapp
```

---

**Note:**  
- Update resource names if you changed them.
- For troubleshooting, check pod logs:

```bash
kubectl logs <pod-name> -n