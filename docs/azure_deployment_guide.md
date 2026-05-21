# AKS FastAPI Deployment Practice Guide

## Objective

Practice deploying a FastAPI application into Azure Kubernetes Service (AKS).

---

# 1. Create a Dedicated Microsoft Dev Account (Optional but Recommended)

Purpose:

* Keeps Azure learning resources separate from personal Microsoft account usage.
* Useful for certifications, labs, and experimentation.

Recommended:

* Create a dedicated Outlook/Hotmail account for Azure practice.

Portal:

* [https://signup.live.com](https://signup.live.com)

---

# 2. Create Azure Free Account

Portal:

* [https://portal.azure.com](https://portal.azure.com)

Purpose:

* Creates Azure account and subscription.

---

# 3. Sign into Azure Portal

Portal:

* [https://portal.azure.com](https://portal.azure.com)

Purpose:

* Access Azure services and manage resources.

---

# 4. Create Azure Free Account

Portal:

* [https://portal.azure.com](https://portal.azure.com)

Purpose:

* Creates Azure account and subscription.

---

# 3. Create Resource Group

Example:

* Resource Group: `abindev-rg`
* Region: `South India`

Purpose:

* Logical grouping of Azure resources.

---

# 4. Install Required Tools

## Azure CLI

Install:

* [https://learn.microsoft.com/cli/azure/install-azure-cli](https://learn.microsoft.com/cli/azure/install-azure-cli)

Verify:

```bash
az version
```

---

## kubectl

Install:

* [https://kubernetes.io/docs/tasks/tools/](https://kubernetes.io/docs/tasks/tools/)

Verify:

```bash
kubectl version --client
```

---

## Docker Desktop

Install:

* [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)

Verify:

```bash
docker version
```

---

# 5. Create AKS Cluster

Node Size - Standard_D2ls_v5

Recommended Beginner Settings:

| Setting                     | Value        |
| --------------------------- | ------------ |
| Pricing Tier                | Free         |
| Node Count                  | 1            |
| VM Size                     | Standard_B2s |
| Auto Scaling                | Disabled     |
| Automatic Upgrade Scheduler | No Schedule  |
| Security Channel Scheduler  | No Schedule  |
| Monitoring                  | Disabled     |
| Azure Defender              | Disabled     |

Purpose:

* Creates managed Kubernetes cluster.

---

# 6. Login to Azure

```bash
az login
```

Purpose:

* Authenticate Azure CLI.

---

# 7. Connect kubectl to AKS Cluster

```bash
az aks get-credentials \
  --resource-group abindev-rg \
  --name aks-practice-cluster
```

Verify:

```bash
kubectl get nodes
```

Purpose:

* Connect local kubectl to AKS.

---
---
C:\Stored Files\Abin\Machine Learning\Projects\Pycharm\azure-aks-deployment>kubectl cluster-info
Kubernetes control plane is running at https://aks-practice-cluster-dns-d09ug7mu.hcp.southindia.azmk8s.io:443
CoreDNS is running at https://aks-practice-cluster-dns-d09ug7mu.hcp.southindia.azmk8s.io:443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://aks-practice-cluster-dns-d09ug7mu.hcp.southindia.azmk8s.io:443/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy
---

# 8. Register Azure Container Registry Provider

If you get:

```text
MissingSubscriptionRegistration
```

Run:

```bash
az provider register --namespace Microsoft.ContainerRegistry
```

Verify:

```bash
az provider show \
  --namespace Microsoft.ContainerRegistry \
  --query registrationState
```

Expected:

```text
"Registered"
```

Purpose:

* Enables Azure Container Registry service.

---

# 9. Create Azure Container Registry (ACR)

```bash
az acr create \
  --resource-group abindev-rg \
  --name akspracticeacr2026abin \
  --sku Basic
```

Purpose:

* Stores Docker images.

---

# 10. Attach ACR to AKS

```bash
az aks update \
  --resource-group abindev-rg \
  --name aks-practice-cluster \
  --attach-acr akspracticeacr2026abin
```

Purpose:

* Allows AKS to pull images securely.

---

# 11. Create FastAPI Application

Project Structure:

```text
fastapi-aks/
├── app/
│   └── main.py
├── requirements.txt
├── Dockerfile
├── deployment.yaml
└── service.yaml
```

---

# 12. FastAPI Code

## app/main.py

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def hello():
    return {"message": "Hello from AKS with FastAPI"}
```

---

# 13. requirements.txt

```text
fastapi
uvicorn[standard]
```

---

# 14. Run Application Locally

Install dependencies:

```bash
pip install -r requirements.txt
```

Run:

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

Open:

```text
http://localhost:8000
```

---

# 15. Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY app app

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Purpose:

* Packages app into container image.

---

# 16. Login to ACR

```bash
az acr login --name akspracticeacr2026abin
## consider the below if the above does not work
az acr login -n akspracticeacr26abin --expose-token

```

---

# 17. Build Docker Image

```bash
docker build -t fastapi-aks:v1 .
```

---

# 18. Tag Docker Image

```bash
docker tag fastapi-aks:v1 \
akspracticeacr2026abin.azurecr.io/fastapi-aks:v1
```

---

# 19. Push Docker Image

```bash
docker push \
akspracticeacr2026abin.azurecr.io/fastapi-aks:v1
```

Purpose:

* Upload image to Azure Container Registry.

---

---
## f. verify image exist in ACR
az acr repository list --name akspracticeacr26abin --output table


# 20. Kubernetes Deployment Manifest

## deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fastapi-deployment

spec:
  replicas: 1

  selector:
    matchLabels:
      app: fastapi-app

  template:
    metadata:
      labels:
        app: fastapi-app

    spec:
      containers:
      - name: fastapi-container
        image: akspracticeacr2026abin.azurecr.io/fastapi-aks:v1

        ports:
        - containerPort: 8000
```

Purpose:

* Creates Kubernetes Pods.

---

# 21. Kubernetes Service Manifest

## service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: fastapi-service

spec:
  type: LoadBalancer

  selector:
    app: fastapi-app

  ports:
  - protocol: TCP
    port: 80
    targetPort: 8000
```

Purpose:

* Exposes application externally.

---

# 22. Deploy to AKS

```bash
kubectl apply -f deployment.yaml
```

```bash
kubectl apply -f service.yaml
```

Purpose:

* Deploys application into AKS cluster.

---

# 23. Verify Deployment

## Check Pods

```bash
kubectl get pods
```

---

## Check Services

```bash
kubectl get svc
```

Wait for External IP.

---

# 24. Access Application

Open:

```text
http://<EXTERNAL-IP>
```

Expected Response:

```json
{
  "message": "Hello from AKS with FastAPI"
}
```

---

# 25. Useful kubectl Commands

## View Deployments

```bash
kubectl get deployments
```

---

## View Pods

```bash
kubectl get pods
```

---

## View Services

```bash
kubectl get svc
```

---

## View Logs

```bash
kubectl logs <pod-name>
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name>
```

---

# 26. Scale Application

```bash
kubectl scale deployment fastapi-deployment --replicas=3
```

Purpose:

* Increase replica count.

---

# 27. Update Application

Rebuild image:

```bash
docker build -t fastapi-aks:v2 .
```

Tag image:

```bash
docker tag fastapi-aks:v2 \
akspracticeacr2026abin.azurecr.io/fastapi-aks:v2
```

Push image:

```bash
docker push \
akspracticeacr2026abin.azurecr.io/fastapi-aks:v2
```

Update deployment:

```bash
kubectl set image deployment/fastapi-deployment \
fastapi-container=akspracticeacr2026abin.azurecr.io/fastapi-aks:v2
```

Purpose:

* Performs rolling update.

---

# 28. View AKS Resources in Azure Portal

Portal:

* [https://portal.azure.com](https://portal.azure.com)

Navigate:

```text
Kubernetes services
→ Your AKS Cluster
→ Kubernetes resources
```

You can view:

* Deployments
* Pods
* Services
* Nodes
* Logs
* YAML manifests

---

# 29. Cleanup Resources

IMPORTANT:
Delete resources after practice to avoid billing.

Delete main resource group ONLY:

```bash
az group delete \
  --name abindev-rg \
  --yes \
  --no-wait
```

Do NOT manually delete:

```text
MC_abindev-rg_aks-practice-cluster_southindia
```

Azure deletes it automatically.

Leave:

```text
NetworkWatcherRG
```

---

---
## Good cleanup checkup
az resource list --output table

# 30. Estimated Cost

If used for a few hours and deleted same day:

Approximate cost:

```text
₹20 – ₹150
```

May be covered by Azure free credits.

---

# Official Documentation

* [https://learn.microsoft.com/azure/aks/](https://learn.microsoft.com/azure/aks/)
* [https://fastapi.tiangolo.com/](https://fastapi.tiangolo.com/)
* [https://kubernetes.io/docs/home/](https://kubernetes.io/docs/home/)
* [https://learn.microsoft.com/azure/container-registry/](https://learn.microsoft.com/azure/container-registry/)
