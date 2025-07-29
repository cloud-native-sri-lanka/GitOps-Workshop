# 🚀 GitOps Workshop: Deploy Microservices using ArgoCD on Kind

Welcome to the GitOps workshop!  
In this hands-on session, you'll learn how to implement GitOps using **ArgoCD**, manage a microservice via **GitHub**, and deploy it to a local **Kubernetes cluster** powered by **Kind** on your personal laptop.

---

## 📋 Prerequisites

Please make sure the following tools are installed and configured **before the session**:

| Tool                  | Version                    | Install Link / Command                                                                 |
| --------------------- | -------------------------- | -------------------------------------------------------------------------------------- |
| **WSL2 (Ubuntu)**     | Ubuntu 20.04+              | [WSL Setup Guide](https://learn.microsoft.com/en-us/windows/wsl/install)               |
| **Docker Desktop**    | Latest (with WSL2 backend) | [Docker](https://www.docker.com/products/docker-desktop)                               |
| **kubectl**           | v1.25+                     | `sudo apt install kubectl`                                                             |
| **Kind**              | v0.20+                     | [Kind Install](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)           |
| **Helm**              | v3.11+                     | `curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash`      |
| **Git**               | Any                        | `sudo apt install git`                                                                 |
| **GitHub Account**    | Any                        | [Sign Up](https://github.com)                                                          |
| **ArgoCD CLI** (optional) | Latest                 | `brew install argocd` or [Download from GitHub](https://github.com/argoproj/argo-cd/releases) |

---
