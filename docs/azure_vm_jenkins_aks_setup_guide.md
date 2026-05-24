# Jenkins + Docker + Azure CLI + AKS Setup Guide on Azure VM

---

# PHASE 1 — Connect to VM

## First get your VM public IP

In Azure Portal:

VM  
→ Overview  
→ Public IP Address

Example:

```bash
20.x.x.x
```

## SSH Into VM

From your terminal:

```bash
ssh azureuser@YOUR_PUBLIC_IP
```

Example:

```bash
ssh azureuser@20.55.10.20
```

Type:

```bash
yes
```

for first connection.

Then enter password.

---

# PHASE 2 — Update Ubuntu

Inside VM:

```bash
sudo apt update && sudo apt upgrade -y
```

---

# PHASE 3 — Install Java

Jenkins requires Java.

Run:

```bash
sudo apt install openjdk-21-jdk -y
```

Verify:

```bash
java -version
```

---

# PHASE 4 — Install Jenkins

Official Docs:

- Jenkins Installation Guide: https://www.jenkins.io/doc/book/installing/linux/

## Add Jenkins Key

```bash
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

## Add Jenkins Repository

```bash
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

## Install Jenkins

```bash
sudo apt update
sudo apt install jenkins -y
```

---

# PHASE 5 — Start Jenkins

Run:

```bash
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

Check status:

```bash
sudo systemctl status jenkins
```

You should see:

```bash
active (running)
```

---

# PHASE 6 — Open Jenkins Port 8080

Now expose Jenkins UI.

In Azure Portal go to:

VM  
→ Networking  
→ Inbound Port Rules  
→ Add Inbound Port Rule

## Add Rule

| Setting | Value |
|---|---|
| Source | Any |
| Source Port | * |
| Destination | Any |
| Service | Custom |
| Destination Port | 8080 |
| Protocol | TCP |
| Action | Allow |
| Priority | 300 |
| Name | allow-jenkins |

Save.

---

# PHASE 7 — Access Jenkins UI

Open browser:

```bash
http://YOUR_PUBLIC_IP:8080
```

Example:

```bash
http://20.x.x.x:8080
```

You should see:

```text
Unlock Jenkins
```

---

# PHASE 8 — Get Initial Admin Password

Back in VM terminal:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy password.

Paste into Jenkins browser page.

---

# PHASE 9 — Install Suggested Plugins

Choose:

```text
Install suggested plugins
```

Wait a few minutes.

---

# PHASE 10 — Create Admin User

Create:

- username
- password
- email

Remember them.

---

# PHASE 11 — Install Docker

Now Jenkins needs Docker.

Official Docs:

- Docker Install Docs: https://docs.docker.com/engine/install/ubuntu/

## Install Docker

Quick install:

```bash
sudo apt install docker.io -y
```

## Start Docker

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

## VERY IMPORTANT

Allow Jenkins to use Docker.

Run:

```bash
sudo usermod -aG docker jenkins
# sudo usermod -aG docker jenkinsvm
```

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

## Verify Docker Works

Test:

```bash
docker ps
```

If permission denied:

Logout and SSH again.

---

# PHASE 12 — Install Azure CLI

Needed for ACR + AKS.

Official Docs:

- Azure CLI Install Guide: https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux

## Quick install

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Verify:

```bash
az version
```

---

# PHASE 13 — Install kubectl

Run:

```bash
sudo az aks install-cli
```

Verify:

```bash
kubectl version --client
```

---

# PHASE 14 — Login to Azure

Run:

```bash
az login
```

It gives a URL and code.

Open URL in browser and authenticate.

---

# PHASE 15 — Connect to AKS

Run:

```bash
az aks get-credentials \
  --resource-group YOUR_RG \
  --name YOUR_AKS_CLUSTER
```

Verify:

```bash
kubectl get nodes
```

You should see AKS nodes.

