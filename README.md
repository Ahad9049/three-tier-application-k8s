# 🚀 Three-Tier MERN App on Kubernetes (KIND + AWS EC2)

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=for-the-badge&logo=jenkins&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white)
![React](https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)

Production-style MERN stack deployed on Kubernetes (KIND) running on AWS EC2 — with CI/CD, autoscaling, ingress, resource controls, and observability.

---

## 🧱 Stack

| Layer | Tech |
|---|---|
| Frontend | React.js + Nginx |
| Backend | Node.js + Express |
| Database | MongoDB Atlas |
| Orchestration | Kubernetes (KIND) |
| Ingress | NGINX Ingress Controller |
| CI/CD | Jenkins → AWS ECR |
| Cloud | AWS EC2, ECS Fargate, IAM |
| Monitoring | Prometheus + Grafana |

---

## 📁 Structure

```
three-tier-application-k8s/
├── frontend/
│   ├── src/
│   ├── Dockerfile
│   └── nginx.conf
├── backend/
│   ├── src/
│   ├── Dockerfile
│   └── .env.example
├── k8s-manifests/
│   ├── autoscaling/
│   │   ├── frontend-hpa.yaml
│   │   └── backend-hpa.yaml
│   ├── backend/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   ├── database/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── secret.yaml
│   ├── frontend/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   ├── ingress/
│   │   └── ingress.yaml
│   ├── limit-ranges/
│   │   ├── frontend-limitrange.yaml
│   │   ├── backend-limitrange.yaml
│   │   └── database-limitrange.yaml
│   ├── namespaces/
│   │   ├── frontend-namespace.yaml
│   │   ├── backend-namespace.yaml
│   │   └── database-namespace.yaml
│   ├── resource-quotas/
│   │   ├── frontend-quota.yaml
│   │   ├── backend-quota.yaml
│   │   └── database-quota.yaml
│   └── storage/
│       ├── pv.yaml
│       └── pvc.yaml
├── jenkins/
│   └── Jenkinsfile
└── README.md
```

---

## ⚡ Quick Start

```bash
# 1. Clone
git clone https://github.com/Ahad9049/three-tier-application-k8s.git
cd three-tier-application-k8s

# 2. Create KIND cluster
kind create cluster --name three-tier --config kind-config.yaml

# 3. Apply all manifests in order
kubectl apply -f k8s-manifests/namespaces/
kubectl apply -f k8s-manifests/limit-ranges/
kubectl apply -f k8s-manifests/resource-quotas/
kubectl apply -f k8s-manifests/storage/
kubectl apply -f k8s-manifests/database/
kubectl apply -f k8s-manifests/backend/
kubectl apply -f k8s-manifests/frontend/

# 4. Install NGINX Ingress Controller
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
kubectl apply -f k8s-manifests/ingress/

# 5. Apply autoscaling
kubectl apply -f k8s-manifests/autoscaling/

# 6. Verify
kubectl get pods,svc,ingress,hpa -A
```

---

## ✨ Key Features

- 🔀 **NGINX Ingress** — path-based routing (`/` → frontend, `/api` → backend)
- 📈 **HPA** — auto-scales pods under CPU load
- 🔒 **Secrets + RBAC** — no hardcoded credentials, least-privilege access
- 📦 **LimitRanges + ResourceQuotas** — prevent resource abuse per namespace
- 💾 **Persistent Storage** — PV + PVC for stateful workloads
- 🔁 **Jenkins CI/CD** — build → push to ECR → deploy automatically

---

## 🙋‍♂️ Author

**Abdul Ahad** — Junior DevOps Engineer | 📍 Islamabad, Pakistan  
[GitHub](https://github.com/Ahad9049) · [Docker Hub](https://hub.docker.com/u/abdulahad9049)

> *"Real DevOps is debugging at midnight and not closing the laptop until it works."*
