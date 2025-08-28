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

### 5. Validate Volume Mounts and ConfigMap/Secret Content

#### **ConfigMap Validation**

List files in `/app/configs` and check their order (lexical order is enforced by naming, e.g., `01-app.yaml`, `02-db.yaml`):

```bash
kubectl exec -n todoapp <pod-name> -- sh -c "ls -1 /app/configs"
```

Verify the contents of each file:

```bash
kubectl exec -n todoapp <pod-name> -- sh -c "for f in /app/configs/*; do echo --- \$f ---; cat \"\$f\"; done"
```

**Expected:**  
- Files appear in lexical order.
- File contents match the ConfigMap data.

#### **Secret Validation**

List files in `/app/secrets`:

```bash
kubectl exec -n todoapp <pod-name> -- sh -c "ls -1 /app/secrets"
```

Verify the contents of each secret file:

```bash
kubectl exec -n todoapp <pod-name> -- sh -c "for f in /app/secrets/*; do echo --- \$f ---; cat \"\$f\"; done"
```

**Expected:**  
- Secret files are present.
- File contents match the Secret data.

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

### 8. Troubleshooting

Check pod logs for errors:

```bash
kubectl logs <pod-name> -n todoapp
```

---

**Note:**  
- Update resource names if you changed them.
- If you use numeric prefixes in ConfigMap keys, files will be ordered lexically in