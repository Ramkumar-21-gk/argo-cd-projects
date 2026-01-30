# ✅ Multi-Cluster Management with ArgoCD

In this section, we will learn how ArgoCD can manage **multiple Kubernetes clusters** from one single control plane.

ArgoCD will run in:

- **kind-argocd-cluster** (Control Plane)

And it will manage:

- **in-cluster** → Dev Cluster (Nginx)
- **argocd-cluster** → Staging Cluster (Apache)
- **prod-kind** → Production Cluster (Online-Shop)

---

# ⭐ Theory

By default, ArgoCD manages only the cluster where it is installed (`in-cluster`).

But using:

```bash
argocd cluster add
```

We can register multiple clusters and deploy apps into them.

✅ This is how enterprises manage:

Dev → Stage → Prod
from one GitOps control plane.

---

# ✅ Benefits

✅ One ArgoCD instance → many clusters
✅ Centralized GitOps deployments
✅ Easy governance and promotion across environments

---

# ✅ Steps to Setup Multi-Cluster in ArgoCD

---

## ✅ Prerequisites

- Kind cluster running (where ArgoCD is installed)
- ArgoCD installed and running
- `kubectl` CLI configured
- `argocd` CLI installed and logged in

📌 Setup Guide:
[ArgoCD Setup & Installation](../../03_setup_installation/README.md)

---

# ✅ Step 1: Create Production Cluster (`prod-kind`)

We will create a new Kind cluster for Production.

Create a file:

📌 `kind_config.yml`

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
networking:
  apiServerAddress: "172.31.19.178" # Change to your EC2 Private IP
  apiServerPort: 33894 # Must be different from argocd-cluster port
nodes:
  - role: control-plane
    image: kindest/node:v1.33.1
```

### Why apiServerAddress and apiServerPort?

To make sure every cluster API server is reachable from ArgoCD pods
and avoids port conflicts.

---

### Create the Cluster

```bash
kind create cluster --name prod-kind --config kind_config.yml
```

---

### Verify Contexts

```bash
kubectl config get-contexts
```

You should see:

- kind-argocd-cluster
- kind-prod-kind

---

# ✅ Step 2: Add Clusters to ArgoCD

Switch back to ArgoCD control plane cluster:

```bash
kubectl config use-context kind-argocd-cluster
```

Now add clusters:

```bash
argocd cluster add kind-argocd-cluster --name argocd-cluster --insecure
argocd cluster add kind-prod-kind --name prod-cluster --insecure
```

---

### Verify Clusters

```bash
argocd cluster list
```

You should see:

- in-cluster
- argocd-cluster
- prod-cluster

Example:

```bash
SERVER                          NAME            STATUS
https://kubernetes.default.svc  in-cluster      Successful
https://172.31.19.178:33893     argocd-cluster  Successful
https://172.31.19.178:33894     prod-cluster    Successful
```

---

# ✅ Step 3: Deploy Nginx in Dev Cluster (`in-cluster`)

Use:

📌 `dev_app.yml`

Replace:

- `<your-username>` with your GitHub username

Apply:

```bash
kubectl apply -f dev_app.yml -n argocd
```

---

# ✅ Step 4: Deploy Apache in Staging Cluster (`argocd-cluster`)

Use:

📌 `stg_app.yml`

Replace:

- `<your-username>`
- `<argocd-cluster-server-url>` from `argocd cluster list`

Apply:

```bash
kubectl apply -f stg_app.yml -n argocd
```

---

# ✅ Step 5: Deploy Online-Shop in Production Cluster (`prod-cluster`)

Use:

📌 `prod_app.yml`

Replace:

- `<your-username>`
- `<prod-cluster-server-url>` from `argocd cluster list`

Apply:

```bash
kubectl apply -f prod_app.yml -n argocd
```

---

# ✅ Step 6: Verify in ArgoCD UI

Go to:

➡️ ArgoCD UI → Applications

You should see:

- nginx-dev → Dev cluster
- apache-stg → Staging cluster
- online-shop-prod → Production cluster

The **CLUSTER** column shows where the app is deployed.

---

# ✅ Step 7: Verify in CLI

List all apps:

```bash
argocd app list
```

Example output:

```bash
nginx-dev         → in-cluster
apache-stg        → argocd-cluster
online-shop-prod  → prod-cluster
```

---

# ✅ Step 8: Check Resources in Each Cluster

### Dev Cluster (in-cluster)

```bash
kubectl get pods,svc -n default
```

---

### Staging Cluster (argocd-cluster)

```bash
kubectl --context kind-argocd-cluster get pods,svc -n default
```

---

### Production Cluster (prod-kind)

```bash
kubectl --context kind-prod-kind get pods,svc -n default
```

✅ Each cluster will have its own application running.

---

# ✅ Step 9: Access Applications

---

## Nginx (Dev)

```bash
kubectl port-forward svc/nginx-service 8081:80 --address=0.0.0.0 &
```

Open:

```text
http://<EC2-Public-IP>:8081
```

---

## Apache (Stage)

```bash
kubectl --context kind-argocd-cluster port-forward svc/apache-service 8082:80 --address=0.0.0.0 &
```

Open:

```text
http://<EC2-Public-IP>:8082
```

---

## Online-Shop (Prod)

```bash
kubectl --context kind-prod-kind port-forward svc/online-shop-service 3000:3000 --address=0.0.0.0 &
```

Open:

```text
http://<EC2-Public-IP>:3000
```

---

# ✅ Key Takeaways

- One ArgoCD instance can manage many Kubernetes clusters
- Add clusters using:

```bash
argocd cluster add
```

- Applications decide cluster using:

```yaml
spec:
  destination:
    server: <cluster-url>
```

✅ Example Multi-Cluster Setup:

- Dev → Nginx
- Stage → Apache
- Prod → Online-Shop
- Control Plane → kind-argocd-cluster

This is the real meaning of **Multi-Cluster GitOps**.

---

✅ Happy Learning!

```

```
