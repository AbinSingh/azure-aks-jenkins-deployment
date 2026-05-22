# PHASE 19 — Create Jenkins Pipeline Job

# Purpose

Create CI/CD automation workflow in Jenkins.

# In Jenkins UI

    Go to:
    
    Dashboard
     → New Item

# Configure
    Setting	Value
    Name	fastapi-aks-pipeline
    Type	Pipeline
    
    Click OK.

# PHASE 20 — Connect Jenkins to GitHub

    Purpose
    
    Allows Jenkins to clone your GitHub repository.
    
    Inside Pipeline Config
    
    Under:
    
        Pipeline
    
    Choose:
    
        Pipeline script from SCM

# Configure
    Field	Value
        SCM	Git
    Repo URL	your GitHub repo
    Branch	main
    Script Path	Jenkinsfile
    
    Save.

# PHASE 21 — Create Jenkinsfile

    Purpose
    
    Defines CI/CD workflow stages.
    
    Create File In GitHub Repo
    Jenkinsfile

# UPDATE Jenkins stages

# In Jenkins UI

Go to:

Manage Jenkins
 → Credentials
 → System
 → Global credentials
 → Add Credentials

# Choose
# Field	Value
Kind	    Username with password
Username	your appId
Password	your secret/password
ID	        azure-sp
Description	Azure Service Principal

# Example
# Field	Value
Username	29d94f1f-b026-4ea6-a02c-41073f4bf5df
Password	your secret
ID	        azure-sp

Save.

# service principal 

-- get subscription id

 az account show --query id -o tsv
 
 subscription id : 625b6bf8-fcc1-4534-9aca-64ed24279425
 
 az ad sp create-for-rbac \
   --name jenkins-sp \
   --role Contributor \
   --scopes /subscriptions/SUBSCRIPTION_ID/resourceGroups/YOUR_RESOURCE_GROUP
 
 az ad sp create-for-rbac \
   --name jenkins-sp \
   --role Contributor \
   **--scopes /subscriptions/625b6bf8-fcc1-4534-9aca-64ed24279425/resourceGroups/**abindev-rg
