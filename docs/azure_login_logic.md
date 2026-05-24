# Microsoft Azure & Entra ID — Core Concepts Summary

## 1. Microsoft Account

Your personal login identity for Microsoft services.

Examples:
- Outlook.com account
- Hotmail account
- Gmail used with Microsoft

Purpose:
- Sign into Microsoft services
- Own Azure subscriptions
- Access Azure Portal
- Access Microsoft Entra ID

---

## 2. Microsoft Entra ID (formerly Azure AD)

Portal:
https://entra.microsoft.com

Purpose:
Identity and authentication system for Microsoft cloud.

It manages:
- Users
- Authentication
- OAuth
- SSO
- App registrations
- Permissions
- Groups
- Security roles

Think of it as:

> Identity & Security Layer

---

## 3. Tenant

A Tenant is an isolated identity workspace inside Entra ID.

Example:
- abindevlabs.onmicrosoft.com

A tenant contains:
- Users
- App registrations
- OAuth settings
- Permissions
- Enterprise apps

Think of it as:

> Your organization workspace

Even personal developers get a tenant.

---

## 4. Tenant ID

A globally unique identifier for the tenant.

Example:
- 173355f5-1151-4db9-a95d-371078021ff7

Used in:
- OAuth
- MSAL
- Azure SDKs
- Azure CLI
- API authentication

Example endpoint:

```text
https://login.microsoftonline.com/<TENANT_ID>
```

---

## 5. Azure Subscription

Purpose:
Billing and resource ownership layer.

A subscription:
- Tracks usage
- Controls billing
- Owns Azure resources

Without a subscription:
- Most Azure services cannot be created

Example:
- Azure Free Account

---

## 6. Azure Free Account

Website:
https://azure.microsoft.com/free

Purpose:
Starter Azure subscription for developers.

Provides:
- Free credits
- Free tier services
- Ability to create Azure resources

Creating an Azure Free Account usually:
- Creates or attaches a subscription
- Automatically creates a default Entra tenant

---

## 7. Azure Portal

Portal:
https://portal.azure.com

Purpose:
Cloud management dashboard.

Used to create/manage:
- Virtual Machines
- Storage
- Databases
- Azure OpenAI
- App Services
- Networking
- Resource Groups

Think of it as:

> Cloud Infrastructure Dashboard

---

## 8. Relationship Between Azure Portal and Entra ID

Azure Portal uses Entra ID for authentication.

Authentication flow:

```text
portal.azure.com
        ↓
login.microsoftonline.com
        ↓
Entra ID authenticates user
        ↓
Azure Portal loads subscriptions/resources
```

Without Entra ID:
- Azure authentication fails
- OAuth fails
- Portal access fails

---

## 9. Resource Group

A Resource Group is an organizational container for Azure resources.

Example:
- abindev-rg

Contains:
- VMs
- Storage
- Databases
- OpenAI services
- App Services

Think of it as:

> Project folder inside Azure

Important:
- Resource Groups do NOT handle authentication
- Authentication happens through Entra ID

---

## 10. Full Azure Architecture Flow

```text
Microsoft Account
        ↓
Entra Tenant
        ↓
Azure Subscription
        ↓
Resource Groups
        ↓
Azure Resources
```

---

## 11. Authentication Flow for Azure Resources

When accessing Azure resources:

```text
Application/User
        ↓
Requests token from Entra ID
        ↓
Token contains tenant identity
        ↓
Azure validates token
        ↓
Access granted
```

Authentication always flows through:
- Entra ID
- Tenant
- Access Tokens

---

## 12. Why Tenant Is Important

The tenant is the foundation for:
- Authentication
- Authorization
- OAuth
- Azure access
- App registrations
- Security

If a tenant becomes inactive:
- Azure Portal login can fail
- OAuth fails
- App registrations stop working

Example error:
- AADSTS5000225

---

## 13. Recommended Setup for Personal Projects

Recommended architecture:

```text
1 Microsoft Account
1 Workforce Tenant
1 Azure Subscription
```

Inside that setup:
- Multiple Resource Groups
- Multiple Projects
- Multiple App Registrations
- Multiple Azure Resources

This is the simplest and most stable setup.
