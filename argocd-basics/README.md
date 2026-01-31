# ✅ Chapter 2: ArgoCD Basics

In this chapter, we will understand the basic concepts of **ArgoCD** and why it is one of the best tools for implementing **GitOps** in Kubernetes.

---

# ✅ 2.1 Why ArgoCD for GitOps?

ArgoCD is one of the most popular GitOps tools because it is designed specially for Kubernetes.

### ✅ Key Reasons to Use ArgoCD

- **Purpose-built for Kubernetes**  
  ArgoCD is a Kubernetes-native controller that works perfectly inside the cluster.

- **Declarative GitOps Tool**  
  Git repository becomes the source of truth for deployments.

- **Multi-Cluster Support**  
  One ArgoCD instance can manage multiple Kubernetes clusters.

- **Powerful UI + CLI + API**  
  ArgoCD provides:
  - Web Dashboard  
  - Command-line tool  
  - REST/gRPC APIs for automation

- **Strong Security**  
  Supports:
  - RBAC (Role Based Access Control)  
  - SSO Login  
  - Audit logs

- **Part of Argo Ecosystem**  
  ArgoCD belongs to the Argo Project family:
  - Argo Workflows  
  - Argo Rollouts  
  - Argo Events  

✅ ArgoCD makes GitOps easy for beginners and strong for production use.

---

# ✅ 2.2 ArgoCD vs FluxCD vs Jenkins X

Below is a simple comparison of popular GitOps tools:

| Tool       | Strengths | Weaknesses |
|-----------|-----------|------------|
| **ArgoCD** | UI, multi-cluster support, app-centric model, notifications | Slightly heavier, mainly focused on Kubernetes |
| **FluxCD** | Lightweight, GitOps-first, strong Helm/Kustomize support | No UI, mostly CLI-based |
| **Jenkins X** | GitOps + CI/CD together, Tekton pipeline integration | Complex, heavy, less popular than ArgoCD/Flux |

✅ ArgoCD is preferred because it provides the most visual and user-friendly experience.

---

# ✅ 2.3 ArgoCD Architecture

ArgoCD has a simple but powerful architecture.

### ✅ Core Components

---

## 1. API Server

- Entry point for ArgoCD UI and CLI  
- Handles:
  - Authentication  
  - RBAC  
  - API Requests  

---

## 2. Repo Server

- Connects to Git repositories  
- Fetches Kubernetes manifests such as:
  - Raw YAML files  
  - Helm Charts  
  - Kustomize Overlays  

- Caches manifests and sends them to Controller

---

## 3. Application Controller

This is the **brain of ArgoCD**.

It continuously compares:

- Desired state (Git)
- Live state (Cluster)

Main responsibilities:

✅ Sync applications  
✅ Detect drift  
✅ Auto-heal resources  
✅ Monitor health  
✅ Support rollbacks  

---

## 4. UI / CLI

- **UI Dashboard** → Visual app monitoring  
- **argocd CLI** → Automation, scripting, operations  

---

# ✅ 2.4 Key ArgoCD Concepts

---

## 1. Application

The most important object in ArgoCD.

An Application connects:

Git Repo → Path → Cluster → Namespace

It represents one deployed Kubernetes app.

---

## 2. Project

Projects group applications logically.

They define:

- Allowed repos  
- Allowed clusters/namespaces  
- RBAC rules  

Example:  
Frontend team can deploy only to `frontend-*` namespaces.

---

## 3. Repositories

Git repos registered with ArgoCD.

They store:

- YAML manifests  
- Helm charts  
- Kustomize configs  

---

## 4. Health Status

ArgoCD shows application health:

- **Healthy** → Everything is running correctly  
- **Degraded** → App has problems (CrashLoopBackOff)  
- **Progressing** → Deployment is happening  
- **Missing** → Resource is expected but not found  

---

## 5. Rollbacks

ArgoCD keeps deployment history.

You can rollback to any previous stable revision easily.

---

## 6. Auto-Healing

If someone manually changes cluster resources, ArgoCD restores them.

Example:

```bash
kubectl delete pod <pod-name>
