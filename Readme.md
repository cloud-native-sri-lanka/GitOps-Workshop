# 📊 GitOps Workshop with ArgoCD, Kind & GitHub

This workshop will guide you through building a local GitOps pipeline using:

- **Kind**: for a local Kubernetes cluster
- **ArgoCD**: to handle GitOps
- **GitHub**: as the source of truth for your app deployment

---

## 🔠 Prerequisites

Make sure you have these installed:

- [Docker](https://www.docker.com/products/docker-desktop)
- [Kind](https://kind.sigs.k8s.io/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/)
- [GitHub CLI (optional)](https://cli.github.com/)
- A GitHub repository with a Kubernetes manifest or Helm chart

🔐 If your repo is private, you’ll also need a **GitHub Personal Access Token (PAT)** with the **`repo`** scope. You can create one at [https://github.com/settings/tokens](https://github.com/settings/tokens).

---

## 1️⃣ Create a Kind Cluster

Install Kind (if not already installed):

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-$(uname)-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

Then create a simple cluster:

```bash
kind create cluster --name gitops-demo
```

> ✉️ This will create a local Kubernetes cluster named `gitops-demo`.

To expose the ArgoCD UI on port 8080 later, we’ll use port forwarding in Step 3.

---

## 2️⃣ Install ArgoCD using Helm

Add the Argo Helm repo:

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
```

Create the namespace:

```bash
kubectl create namespace argocd
```

Now install ArgoCD with GitHub PAT for private repo access:

```bash
helm install argocd argo/argo-cd \
  --namespace argocd \
  --set server.service.type=ClusterIP \
  --set "configs.credentialTemplates.https-creds.url=https://github.com/YOUR_USERNAME" \
  --set "configs.credentialTemplates.https-creds.username=YOUR_GITHUB_USERNAME" \
  --set "configs.credentialTemplates.https-creds.password=YOUR_GITHUB_PAT"
```

> 🛡️ Replace `YOUR_USERNAME`, `YOUR_GITHUB_USERNAME`, and `YOUR_GITHUB_PAT` with your actual values.

> ⚠️ Avoid hardcoding credentials in Helm commands. Consider using Kubernetes Secrets for better security.

---

## 3️⃣ Access ArgoCD UI

Forward port:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:80
```

Open: [http://localhost:8080](http://localhost:8080)

📋 **Default login:**

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d && echo
```

Username: `admin`\
Password: (above output)

---

## 4️⃣ Connect ArgoCD to Your GitHub Repo

Create a `gitops-app.yaml` like below in your repo:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: 'https://github.com/YOUR_USERNAME/YOUR_REPO'
    targetRevision: main
    path: k8s # folder containing manifests or Helm chart
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

Apply it to ArgoCD:

```bash
kubectl apply -f gitops-app.yaml -n argocd
```

ArgoCD will now monitor your GitHub repo and automatically deploy changes!

---

## ✅ 5️⃣ Test Automatic Sync

1. Modify your microservice code or deployment manifest
2. Commit & push the change to GitHub
3. Watch ArgoCD detect and sync the update 🎯

Use ArgoCD UI or:

```bash
kubectl get applications -n argocd
```

---

## 🔄 Optional: Enable Auto-Sync in UI

In ArgoCD UI, go to your app → App Details → "Enable Auto-Sync"

---

## ✅ Done!

You now have a local GitOps pipeline running with:

- Kind (Kubernetes)
- ArgoCD (GitOps controller)
- GitHub (source of truth)

---

## 🛉️ Cleanup

To delete the cluster:

```bash
kind delete cluster --name gitops-demo
```

---

## 📦 Sample Repo Structure

```
📁 your-repo
└── gitops-app.yaml
📅️ k8s
├── deployment.yaml
├── service.yaml

```

---

# 🏢 Enterprise-Style ArgoCD Setup & Directory Structure

In real-world organizations, **application code** and **Kubernetes deployment manifests** are kept in **separate repositories**.

This separation:

- Improves security (developers can’t accidentally change production manifests)
- Keeps infra changes reviewable by platform teams
- Enables CI pipelines to manage deployment promotion
- Allows ArgoCD to only track a **safe, manifest-only repo**


## **Two-Repository Model**

### **A. Application Code Repository**

Contains:

- Source code (`src/`, `services/`, etc.)
- Dockerfile
- `deployment/` folder (developer-owned patches: HPA, resource limits, ingress, etc.)

Example:
📁 my-service-repo
 ├── src/
 ├── Dockerfile
 ├── deployment/
 │   ├── base/                 # Base deployment manifests
 │   │   ├── deployment.yaml
 │   │   ├── service.yaml
 │   │   ├── hpa.yaml
 │   └── overlays/
 │       ├── dev/
 │       │   ├── kustomization.yaml
 │       │   └── patch-deployment.yaml
 │       ├── prod/
 │       │   ├── kustomization.yaml
 │       │   └── patch-deployment.yaml





