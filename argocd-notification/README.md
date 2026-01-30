
# ✅ Chapter 6: ArgoCD Notifications (Easy + Detailed Explanation)

ArgoCD Notifications help us send automatic alerts whenever something happens to our applications.

For example:

✅ Application deployed successfully  
✅ Sync failed  
✅ App health becomes Degraded  
✅ Something breaks in production  

Notifications can be sent to:

- Email  
- Slack  
- Microsoft Teams  
- Webhooks  

So teams can quickly know what is happening inside ArgoCD.

---

# ⭐ 1. What are ArgoCD Notifications?

ArgoCD Notifications is a built-in feature (available from ArgoCD v1.7+).

It continuously watches all Applications running in ArgoCD.

Whenever an important event occurs, it sends a message automatically.

---

## ✅ Why Notifications are Important?

Without notifications:

- You must open ArgoCD UI again and again  
- Problems may go unnoticed  
- Teams won’t know if deployments failed  

With notifications:

✅ Instant alerts  
✅ Faster debugging  
✅ Better GitOps monitoring  
✅ Useful for DevOps teams

---

# ⭐ 2. Main Components of Notifications

ArgoCD Notifications works using 4 main parts:

---

## 1. Notification Controller

This is a Kubernetes controller running inside ArgoCD.

Its job:

- Watch Applications  
- Detect events (Healthy, Degraded, Sync Failed)  
- Trigger notifications  

---

## 2. Triggers (WHEN to send)

Triggers decide **when** to send notification.

Examples:

- Send email when sync fails  
- Send email when app becomes degraded  
- Send message when deployment succeeds  

Trigger Example:

```text
on-sync-failed
on-health-degraded
on-deployed
````

---

## 3. Templates (WHAT to send)

Templates decide the message format.

Example:

* Email Subject
* Email Body
* Slack Message

So the message looks clean and useful.

---

## 4. Subscriptions (WHERE to send)

Subscriptions decide the destination:

* Email address
* Slack channel
* Teams group

Example:

```yaml
notifications.argoproj.io/subscribe.on-health-degraded.email: user@gmail.com
```

This means:

➡️ Send degraded alert to this email.

---

# ✅ Common Use Cases

✅ Email when application sync fails
✅ Slack message when deployment succeeds
✅ Alert when app becomes unhealthy
✅ Notify platform teams during production failures

---

# ✅ 3. Prerequisites

Before configuring notifications, you need:

* Kind cluster running
* ArgoCD installed and running
* `kubectl` CLI configured
* `argocd` CLI installed and logged in

Also you need:

✅ SMTP server to send emails

We will use Gmail SMTP.

---

# ✅ Gmail App Password Setup

Gmail does not allow normal password for SMTP.

So you must create an **App Password**.

Steps:

1. Enable **2-Step Verification**
2. Open:

```text
https://myaccount.google.com/apppasswords
```

3. Create App Password with name:

```text
ArgoCD SMTP
```

4. Copy the generated 16-character password

📌 Video Guide:
[https://youtu.be/s-7DBjS3XRg](https://youtu.be/s-7DBjS3XRg)

---

# ✅ 4. Setup: Sending Email Notifications

Now we will configure ArgoCD to send emails.

---

# ✅ Step 0: Install Default Templates + Triggers

ArgoCD provides a catalog of ready templates.

Install them:

```bash
kubectl apply -n argocd -f \
https://raw.githubusercontent.com/argoproj/argo-cd/stable/notifications_catalog/install.yaml
```

✅ This installs built-in triggers like:

* on-sync-failed
* on-deployed
* on-health-degraded

---

# ✅ Step 1: Configure SMTP Secret

We need to provide Gmail credentials securely.

So we create a Kubernetes Secret.

📌 File: `secret-smtp.yaml`

Replace:

* sender email
* Gmail app password

Apply:

```bash
kubectl apply -f secret-smtp.yaml
```

---

## ✅ Why Secret?

Because SMTP username/password is sensitive.

✅ Secret keeps credentials safe
❌ Do not store passwords inside GitHub repo

---

# ✅ Step 2: Configure Notifications ConfigMap

Now we define notification logic inside ConfigMap.

File:

📌 `argocd-notifications-cm.yaml`

It contains:

* Email notifier settings
* Templates
* Triggers

Apply:

```bash
kubectl apply -f argocd-notifications-cm.yaml
```

---

## ✅ Secret + ConfigMap Working (Easy Flow)

1. Secret stores:

```text
email-username
email-password
```

2. ConfigMap references them:

```text
$email-username
$email-password
```

3. Notification Controller automatically connects both.

✅ Secure + GitOps friendly

---

# ✅ Step 3: Subscribe Application to Notifications

Now we tell ArgoCD:

➡️ Send alerts for this application.

We use annotations inside Application YAML.

File:

📌 `chai-app.yaml`

Example annotation:

```yaml
metadata:
  annotations:
    notifications.argoproj.io/subscribe.on-deployed.email: receiver@gmail.com
    notifications.argoproj.io/subscribe.on-health-degraded.email: receiver@gmail.com
```

Meaning:

✅ Email when deployed
✅ Email when degraded

Apply:

```bash
kubectl apply -f chai-app.yaml
```

---

# ✅ Step 4: Demo Testing

---

## ✅ Case 1: Deployment Success Alert

When chai-app becomes Healthy:

✅ You will receive deployment success email.

---

## ✅ Case 2: Degraded Alert

Now create a failure:

1. Change image to wrong tag:

```yaml
image: nginx:v1   # tag does not exist
```

2. Commit + Push

3. ArgoCD will try to sync

4. App becomes:

❌ Degraded

Then notification is triggered.

---

### Check Controller Logs

```bash
kubectl -n argocd logs deploy/argocd-notifications-controller --follow
```

Once degraded → email sent automatically.

---

# ✅ Example Email Subject

```text
[ArgoCD] chai-app health is Degraded
```

Email body contains app details and error info.

---

# ✅ 5. Common Notification Triggers

| Trigger Name         | Meaning                     |
| -------------------- | --------------------------- |
| `on-sync-succeeded`  | Sync completed successfully |
| `on-sync-failed`     | Sync failed due to error    |
| `on-health-degraded` | App health becomes Degraded |
| `on-deployed`        | App deployed and Healthy    |
| `on-created`         | New app created             |
| `on-deleted`         | App removed                 |

---

# ✅ Final Key Takeaways

* ArgoCD Notifications provide alerting system for GitOps
* Helps teams know immediately if deployment succeeds or fails
* Email setup needs:

1. Secret (SMTP credentials)
2. ConfigMap (templates + triggers)
3. Application annotations (subscriptions)

✅ Very useful for production monitoring

---

📌 Official Docs:
[https://argo-cd.readthedocs.io/en/stable/operator-manual/notifications/](https://argo-cd.readthedocs.io/en/stable/operator-manual/notifications/)

---

✅ Happy Learning!

```
```
