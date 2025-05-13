# 🚀 Terraform GKE Infrastructure with Monitoring and Ingress

This repository automates the provisioning of a **Google Kubernetes Engine (GKE)** cluster using **Terraform**, and includes instructions for installing monitoring and ingress manually using Helm.

---

## 📁 Project Structure

```
.
terraform
├── argocd.tf
├── gke.tf
├── kubernetes-dashboard-admin.rbac.yaml
├── outputs.tf
├── terraform.tfvars
├── versions.tf
├── vpc.tf
apps  
├── kube-prometheus-values.yaml
├── nginx-ingress-values.yaml
README.md
```

---

## 1️⃣ Provision GKE Cluster with Terraform

### 🧱 Required Files

- `gke.tf` – Defines the GKE cluster and node pool
- `vpc.tf` – Creates the VPC and subnet for the cluster
- `argocd.tf` – Installs ArgoCD via Helm
- `outputs.tf` – Outputs cluster information
- `terraform.tfvars` – Variable values
- `versions.tf` – Provider and version lock
- `kubernetes-dashboard-admin.rbac.yaml` – Admin access for Kubernetes dashboard

### ▶️ Steps to Apply

```bash
# Initialize
terraform init

# Plan
terraform plan

# Apply
terraform apply 
```

> After creation, configure `kubectl`:
```bash
gcloud container clusters get-credentials abiding-casing-457600-s0-gke --zone us-east1-b
```

---

## 2️⃣ Manually Install Kube-Prometheus-Stack

### 📦 Add Helm Repos

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

```bash
kubectl create namespace monitoring

helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack   --namespace monitoring   -f kube-prometheus-values.yaml
```

> Access **Grafana**:
```bash
kubectl get svc -n monitoring
```

- Username: `admin`
- Password: `admin123`
- Login URL: `http://<EXTERNAL-IP>`

---

## 3️⃣ Manually Install NGINX Ingress Controller

### 📦 Add Helm Repo

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

### 📥 Install using `nginx-ingress-values.yaml`

```bash
kubectl create namespace ingress-nginx

helm install ingress-nginx ingress-nginx/ingress-nginx   --namespace ingress-nginx   -f nginx-ingress-values.yaml
```

> Check the external IP of the ingress controller:
```bash
kubectl get svc -n ingress-nginx
```

---

## 📌 Notes

- Ensure GCP quota allows SSD, LoadBalancer, and CPU resources for node pools.
- ArgoCD and Grafana are exposed via LoadBalancer services by default.
- Use `kubectl port-forward` as an alternative if external IP is unavailable.

---

## 📬 Feedback

Pull requests and issues are welcome for enhancements or fixes!
