# 🚀 Three-Tier MERN Application on Kubernetes (KIND + AWS EC2)

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=for-the-badge&logo=jenkins&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white)
![React](https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)

A production-style deployment of a full Three-Tier MERN application on a Kubernetes cluster (KIND) running on AWS EC2. This project demonstrates real-world DevOps practices including container orchestration, CI/CD automation, security hardening, and observability.

---

## 📐 Architecture Overview

```
                          ┌─────────────────────────────────────────┐
                          │          AWS EC2 Instance (Ubuntu)       │
                          │                                          │
                          │   ┌──────────────────────────────────┐   │
                          │   │     KIND Kubernetes Cluster      │   │
                          │   │                                  │   │
                          │   │  ┌──────────┐  ┌─────────────┐  │   │
                          │   │  │ frontend │  │   backend   │  │   │
                          │   │  │namespace │  │  namespace  │  │   │
                          │   │  │          │  │             │  │   │
                          │   │  │ React    │  │ Node.js     │  │   │
                          │   │  │ (HPA)    │  │ Express     │  │   │
                          │   │  └────┬─────┘  └──────┬──────┘  │   │
                          │   │       │                │         │   │
                          │   │       └──────┬─────────┘         │   │
                          │   │             │                    │   │
                          │   │      ┌──────▼──────┐             │   │
                          │   │      │  MongoDB    │             │   │
                          │   │      │   Atlas     │             │   │
                          │   │      │ (External)  │             │   │
                          │   │      └─────────────┘             │   │
                          │   └──────────────────────────────────┘   │
                          └─────────────────────────────────────────┘
                                            │
                                    Jenkins CI/CD
                                    (Build → Push → Deploy)
                                            │
                                    ┌───────▼───────┐
                                    │  AWS ECR /    │
                                    │  ECS Fargate  │
                                    └───────────────┘
```

---

## 🧱 Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | React.js (multi-stage Docker build, Nginx) |
| **Backend** | Node.js + Express.js |
| **Database** | MongoDB Atlas (managed, external) |
| **Container Runtime** | Docker |
| **Orchestration** | Kubernetes (KIND on AWS EC2) |
| **CI/CD** | Jenkins Pipeline |
| **Container Registry** | AWS ECR / GitHub Container Registry |
| **Cloud** | AWS EC2, ECS Fargate, IAM, RDS |
| **Monitoring** | Prometheus + Grafana |
| **Logging** | EFK Stack (Elasticsearch + Fluentd + Kibana) |
| **Security** | K8s Secrets, RBAC, NetworkPolicies |

---

## ✨ Key Features

- **Namespace Isolation** — Frontend, backend, and database run in separate Kubernetes namespaces for clean separation of concerns
- **Horizontal Pod Autoscaler (HPA)** — Stateless React and Node.js services auto-scale under load
- **K8s Secrets** — No hardcoded credentials anywhere in the codebase
- **RBAC** — Role-Based Access Control restricts what each service can access
- **NetworkPolicies** — Explicit allow/deny rules between namespaces; nothing talks to what it shouldn't
- **Automated CI/CD** — Jenkins pipeline handles build, test, push to ECR, and deploy end-to-end
- **Full Observability** — Prometheus scrapes metrics, Grafana visualizes them, EFK centralizes logs
- **MongoDB Atlas** — Stateful data offloaded to a managed cloud database (the right way)

---

## 📁 Project Structure

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
├── k8s/
│   ├── namespaces/
│   │   ├── frontend-namespace.yaml
│   │   ├── backend-namespace.yaml
│   │   └── database-namespace.yaml
│   ├── frontend/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   ├── backend/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
├── jenkins/
│   └── Jenkinsfile
└── README.md
```

---

## ⚙️ Prerequisites

- AWS EC2 instance (Ubuntu 22.04 recommended, t2.medium or higher)
- Docker installed
- KIND installed
- kubectl installed
- Jenkins installed and running
- AWS CLI configured with IAM credentials
- MongoDB Atlas account and cluster URI
- AWS ECR repository created

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/Ahad9049/three-tier-application-k8s.git
cd three-tier-application-k8s
```

### 2. Create the KIND Cluster

```bash
kind create cluster --name three-tier --config kind-config.yaml
kubectl cluster-info --context kind-three-tier
```

### 3. Create Namespaces

```bash
kubectl apply -f k8s/namespaces/
```

### 4. Apply Secrets (update with your values first)

```bash
# Edit the secret file with your base64-encoded credentials
kubectl apply -f k8s/secrets/db-secret.yaml
```

### 5. Apply RBAC and NetworkPolicies

```bash
kubectl apply -f k8s/rbac/
kubectl apply -f k8s/network-policies/
```

### 6. Deploy Frontend, Backend

```bash
kubectl apply -f k8s/frontend/
kubectl apply -f k8s/backend/
```

### 7. Verify Everything is Running

```bash
kubectl get pods -A
kubectl get svc -A
kubectl get hpa -A
```

---

## 🔁 Jenkins CI/CD Pipeline

The Jenkins pipeline automates the full workflow:

```
Code Push → Build Docker Images → Push to ECR → Deploy to K8s / ECS Fargate
```

**Pipeline Stages:**
1. `Checkout` — Pull latest code from GitHub
2. `Build` — Docker multi-stage build for frontend and backend
3. `Push` — Tag and push images to AWS ECR
4. `Deploy` — Apply updated K8s manifests or trigger ECS Fargate deployment
5. `Notify` — Pipeline status notification

**Jenkinsfile location:** `jenkins/Jenkinsfile`

---

## 📊 Monitoring & Logging

### Prometheus + Grafana

```bash
kubectl apply -f k8s/monitoring/prometheus/
kubectl apply -f k8s/monitoring/grafana/

# Access Grafana dashboard
kubectl port-forward svc/grafana 3000:3000 -n monitoring
```

Open `http://localhost:3000` — default credentials: `admin/admin`

### EFK Stack (Elasticsearch + Fluentd + Kibana)

Centralized logging for all pods across namespaces. Fluentd runs as a DaemonSet to collect logs from every node.

---

## 🔐 Security Highlights

| Security Control | Implementation |
|---|---|
| No hardcoded secrets | All credentials stored in K8s Secrets |
| Least privilege access | RBAC roles scoped per namespace |
| Network segmentation | NetworkPolicies deny all by default, allow explicitly |
| IAM roles | EC2 instance role for ECR access, no static keys |
| Multi-stage builds | Minimal final Docker images, no dev dependencies |

---

## 🌐 AWS Services Used

- **EC2** — Hosts the KIND Kubernetes cluster
- **ECR** — Private Docker image registry
- **ECS Fargate** — Serverless container deployment (alternative deployment target)
- **IAM** — Roles and policies for secure service access
- **RDS** — (Optional) relational database layer

---

## 🧠 Lessons Learned

- KIND clusters on EC2 need proper memory sizing — use at least `t2.medium`
- Docker socket permissions must be configured correctly for Jenkins to build images
- IAM service-linked roles for ECS must exist before the first Fargate deployment
- NetworkPolicies require a CNI plugin that supports them (Calico recommended with KIND)
- Multi-stage Docker builds significantly reduce final image size and attack surface

---

## 🙋‍♂️ Author

**Abdul Ahad**  
Junior DevOps Engineer | Cloud & Infrastructure Enthusiast  
📍 Islamabad, Pakistan  
🔗 [GitHub](https://github.com/Ahad9049) | [LinkedIn](https://linkedin.com/in/your-profile)  
🐳 [Docker Hub](https://hub.docker.com/u/abdulahad9049)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

> *"Real DevOps experience isn't about watching tutorials — it's about debugging at midnight and not closing the laptop until it works."*
