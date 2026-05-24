5. Authentication flow internally

Suppose you open Azure Portal.

portal.azure.com
        ↓
redirects to login.microsoftonline.com
        ↓
Entra ID authenticates you
        ↓
returns token
        ↓
Azure Portal reads:
- tenant
- roles
- subscriptions
- permissions

6. When APIs/services are called

Same concept.

Example:

DefaultAzureCredential()

Internally:

gets token from Entra ID
token includes tenant info
Azure verifies token