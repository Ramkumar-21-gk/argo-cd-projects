

# ⭐ What is App of Apps Pattern?

Normally in ArgoCD:

- One application = One `Application` CRD manifest  
- So if you have 10 apps → you create 10 Application YAML files  

But in real projects, teams manage:

✅ Dozens of apps  
✅ Microservices  
✅ Platform components  

So ArgoCD provides the **App of Apps Pattern**.

---

# ✅ How it Works

App of Apps means:

- You create one **Root Application**
- Root app points to a Git folder
- That folder contains multiple **Child Application manifests**
- ArgoCD syncs Root app → then automatically creates all child apps

---

## ✅ Benefits of App of Apps

✅ One entry point to manage many apps  
✅ Easy onboarding for new teams  
✅ Reusable Git structure (DRY principle)  
✅ Perfect for platform and enterprise environments  
✅ Central place for managing all apps  

---

# ✅ Steps to Apply App of Apps Pattern

---

## ✅ Prerequisites

Before starting, ensure:

- ArgoCD is installed and running  
- `kubectl` is configured  
- `argocd` CLI is installed  
- Kind cluster is added to ArgoCD  

📌 Setup Guide:  
[ArgoCD Setup & Installation](../../03_setup_installation/README.md)

---

# ✅ Step 1: Review Root Application Manifest

The root application is the parent app.

Use:

📌 `root_app.yml`

Replace:

- `<your-username>` with your GitHub username  
- `<cluster-url>` with your added ArgoCD cluster server URL  

You can get cluster URL by running:

```bash
argocd cluster list
````

---

### Important Note

In your Git repo folder:

```text
app_of_apps/apps/
```

Update all child apps (`nginx`, `apache`, `online-shop`) with your correct cluster URL:

Example:

```yaml
server: https://<your-cluster-url>
```

Commit and push changes after updating.

---

# ✅ Step 2: Apply Root Application

Now apply root app:

```bash
kubectl apply -f root_app.yml -n argocd
```

This will create the **root-app** inside ArgoCD.

---

# ✅ Step 3: Verify in ArgoCD UI

Go to:

➡️ ArgoCD UI → Applications

You will see:

✅ `root-app`

Expanding it shows child apps:

* nginx-child
* apache-child
* online-shop-child

ArgoCD automatically manages all apps under root.

---

# ✅ Step 4: Verify Using CLI

Run:

```bash
argocd app list
```

You will see output like:

```bash
root-app
nginx-child
apache-child
online-shop-child
```

✅ All apps are controlled by the root app.

---

### Note About `online-shop-app`

If an Application CRD already exists in your Git repo,
ArgoCD may create it automatically as part of true GitOps.

---

# ✅ Step 5: Test Sync Feature

To test:

1. Modify a child app YAML (example: change replicas)
2. Commit and push changes
3. Sync root app

Example:

* Replicas changed from 5 → 3
  ✅ Root sync updates child apps automatically

---

# ✅ Access All Child Applications

---

## Check Pods

Run:

```bash
kubectl get pods
```

You should see pods of:

✅ nginx
✅ apache
✅ online-shop

---

## Check Services

Run:

```bash
kubectl get svc
```

You will see services for all child apps.

---

# ✅ Port Forward to Access Apps

---

## 1. Nginx App

```bash
kubectl port-forward svc/nginx-service 8081:80 --address=0.0.0.0 &
```

Access:

```text
http://<EC2-Public-IP>:8081
```

---

## 2. Apache App

```bash
kubectl port-forward svc/apache-service 8082:80 --address=0.0.0.0 &
```

Access:

```text
http://<EC2-Public-IP>:8082
```

---

## 3. Online Shop App

```bash
kubectl port-forward svc/online-shop-service 3000:3000 --address=0.0.0.0 &
```

Access:

```text
http://<EC2-Public-IP>:3000
```

---

# ✅ Key Takeaways

* App of Apps = One **Root App** managing many **Child Apps**
* Best for platform teams managing many workloads
* Root app sync automatically controls all child apps
* Recommended approach for large GitOps setups

✅ Best Practice:
Keep root app definitions in a separate **platform repo**.

---

