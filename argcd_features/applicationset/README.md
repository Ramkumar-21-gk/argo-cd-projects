````md
# ✅ ApplicationSets in ArgoCD

In this section, we will learn about **ApplicationSets** in ArgoCD.

ApplicationSets help us create **multiple Applications automatically** using a single YAML file.

Instead of writing many `Application` manifests, we define:

✅ One template  
✅ One generator  
➡️ ArgoCD creates all apps automatically

---

# ⭐ What is an ApplicationSet?

Normally:

- One app → One `Application` YAML

But in real setups, we manage:

- Many microservices  
- Multiple environments (Dev, Stg, Prod)  
- Multiple clusters  

So ApplicationSets solve this problem.

---

# ✅ Why ApplicationSets are Useful?

✅ Less YAML files  
✅ Automatic app creation  
✅ Easy multi-cluster deployments  
✅ True DRY (Don’t Repeat Yourself)

---

# ✅ Types of Generators

Generators decide **how apps are created**:

---

## 1. List Generator

- Static list of apps (fixed apps)

Example creates:

- nginx-list  
- online-shop-list  
- chaiapp-list  

---

## 2. Git Generator

- Scans folders inside a Git repo  
- Creates one app per folder

Best for monorepo + microservices

---

## 3. Cluster Generator

- Deploys the same app into **all clusters** registered in ArgoCD

Best for:

- Dev / Staging / Production clusters

---

# ✅ Steps to Use ApplicationSets

---

# ✅ 1. List Generator Example

## Prerequisites

- Kind cluster running  
- ArgoCD installed  
- `kubectl` + `argocd` CLI configured  

---

## Step 1: Apply List Generator Manifest

Use:

📌 `list_generator.yml`

Replace:

- `<your-username>` with your GitHub username

Apply:

```bash
kubectl apply -f list_generator.yml -n argocd
````

---

## Step 2: Verify ApplicationSet

Run:

```bash
argocd appset list
```

---

## Step 3: Verify Apps Created

ArgoCD automatically creates:

✅ nginx-list
✅ online-shop-list
✅ chaiapp-list

Check:

```bash
argocd app list
```

---

## Step 4: Access Apps (Port Forward)

### Nginx

```bash
kubectl port-forward svc/nginx-service 8081:80 --address=0.0.0.0 &
```

Open:

```text
http://localhost:8081
```

---

### Online Shop

```bash
kubectl port-forward svc/online-shop-service 3000:3000 --address=0.0.0.0 &
```

Open:

```text
http://localhost:3000
```

---

### Chai App

```bash
kubectl port-forward svc/chai-app-service 3001:3000 --address=0.0.0.0 &
```

Open:

```text
http://localhost:3001
```

---

## Delete ApplicationSet

```bash
argocd appset delete argocd/demo-list
```

---

# ✅ 2. Cluster Generator Example

Cluster generator deploys the same app to **all clusters**.

## Extra Requirement

At least 2 clusters registered in ArgoCD.

Example:

* in-cluster
* argocd-cluster

Add cluster:

```bash
argocd cluster add kind-argocd-cluster
```

---

## Apply Cluster Generator Manifest

Use:

📌 `cluster_generator.yml`

Apply:

```bash
kubectl apply -f cluster_generator.yml -n argocd
```

---

## Verify Apps

```bash
argocd app list
```

You will see apps created for each cluster:

✅ in-cluster-chai-app
✅ kind-argocd-cluster-chai-app

---

## Access App

```bash
kubectl port-forward --context kind-argocd-cluster svc/chai-app-service 3002:3000 --address=0.0.0.0 &
```

Open:

```text
http://localhost:3002
```

---

## Delete ApplicationSet

```bash
argocd appset delete argocd/demo-cluster
```

---

# ✅ 3. Git Generator Example

Git generator creates apps by scanning folders inside Git repo.

## Requirement

Your repo should have multiple app folders like:

```text
git_generator/
  apache/
  online-shop/
  chai-app/
```

---

## Apply Git Generator Manifest

Use:

📌 `git_generator.yml`

Apply:

```bash
kubectl apply -f git_generator.yml -n argocd
```

---

## Verify Apps Created

```bash
argocd app list
```

Apps created automatically:

✅ apache-git
✅ online-shop-git
✅ chai-app-git

---

## Delete ApplicationSet

```bash
argocd appset delete argocd/demo-git
```

---

# ✅ Generator Comparison

| Generator Type        | Works By                       | Best For                   |
| --------------------- | ------------------------------ | -------------------------- |
| **List Generator**    | Fixed list of apps             | Small known set of apps    |
| **Cluster Generator** | Deploy app to all clusters     | Multi-cluster environments |
| **Git Generator**     | Scan Git folders automatically | Microservices + Monorepos  |

---

# ✅ Key Takeaways

* **ApplicationSet = One YAML → Many Applications**
* Generators help create apps automatically
* Best for large GitOps and enterprise deployments
* Reduces duplication and simplifies management

📌 Official Docs:
[https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/](https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/)

---

✅ Happy Learning!

```
```
