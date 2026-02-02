
# ✅ Chapter 9: Security & Scaling in ArgoCD (Easy + Proper Explanation)

When you start using ArgoCD in real production environments, two things become very important:

✅ Security (Who can access what?)  
✅ Scalability (How ArgoCD works with many apps and teams?)

This chapter explains:

- User Management  
- RBAC (Role Based Access Control)  
- Local Users and Admin User  
- SSO Login with GitHub (OIDC)  
- High Availability (HA) Scaling  
- Enterprise GitOps Best Practices  

---

# ✅ Prerequisites

Before starting, make sure you have:

- Kubernetes cluster running  
- ArgoCD installed and working  
- `kubectl` configured  
- `argocd` CLI installed and logged in  
- Helm installed  
- GitHub access (for SSO configuration)

📌 Setup Guide:  
[ArgoCD Setup](../03_setup_installation/README.md)

---

# ✅ 1. User Management in ArgoCD

---

## ✅ Built-in Admin User

ArgoCD comes with one default user:

```text
admin
````

This user has full permissions.

### Best Practice

✅ Use admin only for initial setup
❌ Do not keep using admin forever

In production:

* Create new users
* Disable admin account

---

## ✅ Local Users vs SSO Users

ArgoCD supports two types of users:

---

### ✅ Local Users

Local users are created inside ArgoCD itself.

Used for:

* Small teams
* CI/CD automation tokens
* Simple setups

Example:

```text
alice
bob
```

---

### ✅ SSO Users (Recommended for Enterprises)

SSO means:

* Login using GitHub / Google / Okta
* Centralized identity management
* Group-based access

Best for:

✅ Large organizations
✅ Multi-team setups

---

# ✅ 2. RBAC in ArgoCD (Role Based Access Control)

---

## ⭐ What is RBAC?

RBAC controls:

* WHO can do actions
* WHAT actions they can do
* ON which resources

Example:

✅ Alice can view apps
❌ Alice cannot delete apps
✅ Bob can sync apps
✅ Admin has full access

RBAC is configured inside:

```text
argocd-rbac-cm ConfigMap
```

---

## ✅ RBAC Components

### Role

A role is a set of permissions.

Example:

```text
role:readonly
role:admin
role:developer
```

---

### Policy

Policy defines what a role is allowed to do.

Example:

```text
Allow sync
Deny delete
Allow get
```

---

### Subject

A subject is the user or group assigned to a role.

Example:

```text
alice → role:readonly
bob   → role:admin
```

---

## ✅ RBAC Syntax (Casbin)

ArgoCD uses Casbin policy format:

---

### Group Assignment

```text
g, <user/group>, <role>
```

Example:

```text
g, alice, role:readonly
g, bob, role:admin
```

---

### Policy Assignment

```text
p, <role>, <resource>, <action>, <object>, <effect>
```

Example:

```text
p, role:readonly, applications, get, */*, allow
p, role:readonly, applications, delete, */*, deny
```

---

## ✅ Common Resources and Actions

RBAC can control:

* Applications
* Projects
* Clusters
* Repositories
* ApplicationSets

Actions include:

✅ get, create, update, delete, sync

---

# ✅ Creating Local Users

Local users are created inside:

```text
argocd-cm ConfigMap
```

File:

📌 `argocd-user-cm.yaml`

Apply:

```bash
kubectl apply -f argocd-user-cm.yaml
```

---

## ✅ Set Password for Users

```bash
argocd account update-password --account alice
argocd account update-password --account bob
```

---

## ✅ Verify Users

List users:

```bash
argocd account list
```

Get details:

```bash
argocd account get --account alice
```

---

## ✅ Disable Admin User (Recommended)

After creating secure users, disable admin:

```bash
kubectl patch -n argocd configmap argocd-cm \
--patch='{"data":{"admin.enabled":"false"}}'
```

✅ Improves production security

---

# ✅ Example RBAC Policy

```yaml
data:
  policy.csv: |
    # Readonly role
    p, role:readonly, applications, get, */*, allow
    p, role:readonly, applications, sync, */*, deny

    # Admin role
    p, role:admin, applications, *, */*, allow

    # Bind users
    g, alice, role:readonly
    g, bob, role:admin

  policy.default: role:readonly
```

---

## ✅ Best Practices for RBAC

✅ Default should be readonly
✅ Give minimum required permissions
✅ Prefer groups instead of individual users

---

# ✅ 3. SSO with Dex / OIDC

SSO allows login with:

* GitHub
* Google
* Okta

Instead of managing passwords manually, teams login using identity provider.

✅ Best for enterprise environments

📌 Guide: [SSO Setup](github_sso.md)

---

# ✅ 4. Scaling ArgoCD for High Availability (HA)

---

## ⭐ Why Scaling is Needed?

In production, you may have:

* Hundreds of applications
* Many teams
* Multiple clusters

So ArgoCD must be highly available.

---

## ✅ What is High Availability (HA)?

HA means:

✅ ArgoCD keeps running even if one pod crashes
✅ No single point of failure
✅ Recommended for production deployments

---

## ✅ ArgoCD HA Components

* API Server (multiple replicas)
* Repo Server (scales horizontally)
* Application Controller (leader election)
* Redis HA (cache in HA mode)

---

## ✅ Install ArgoCD HA Manifests

Official HA setup:

```bash
kubectl apply -n argocd -f \
https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/ha/install.yaml
```

---

## ✅ Scale Components Manually

```bash
kubectl -n argocd scale deployment argocd-server --replicas=3
kubectl -n argocd scale deployment argocd-repo-server --replicas=2
kubectl -n argocd scale statefulset argocd-application-controller --replicas=2
```

---

## ✅ Verify Pods

```bash
kubectl get pods -n argocd
```

---

## ✅ Redis HA Check

```bash
kubectl get pods -n argocd | grep redis
```

---

## ✅ HA Best Practices

* Spread pods across nodes (Anti-affinity)
* Set CPU/Memory limits
* Use external PostgreSQL for large scale
* Use LoadBalancer/Ingress properly

📌 Docs:
[https://argo-cd.readthedocs.io/en/latest/operator-manual/high_availability/](https://argo-cd.readthedocs.io/en/latest/operator-manual/high_availability/)

---

# ✅ 5. GitOps Best Practices for Enterprises

---

## ✅ Security Best Practices

* Use SSO only in production
* Avoid local passwords
* Enable TLS everywhere
* Use Secret Managers (Vault, Sealed Secrets)
* Apply least privilege RBAC
* Enable audit logs

---

## ✅ Scaling Best Practices

* Use AppProjects for team isolation
* Use ApplicationSets for templated apps
* Use Sync Waves for ordering
* Apply Resource Quotas
* Separate management cluster from workload clusters

---

## ✅ Operational Best Practices

* Git = Single Source of Truth
* Use PR-based review workflows
* Promote apps Dev → Stage → Prod
* Enable monitoring (Prometheus + Grafana)
* Take backups for disaster recovery

---

# ✅ Final Summary (Chapter 9)

* Security is critical when ArgoCD runs in production
* RBAC ensures correct user permissions
* Local users are useful for automation
* SSO is best for enterprise environments
* HA scaling is required for large setups
* Follow GitOps best practices for secure scaling

---

✅ Happy Learning!

```
```
