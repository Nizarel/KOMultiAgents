# GitHub Actions Deployment Setup Guide - OIDC Authentication

This guide helps you set up Azure authentication for GitHub Actions using OpenID Connect (OIDC) to deploy your containerized applications to Azure Container Apps.

## Prerequisites

- Azure CLI installed and logged in
- GitHub repository with admin access
- Azure subscription with appropriate permissions
- Existing Azure App Registration: `id-3rbky3yz3zumq`

## Step 1: Configure Azure App Registration for OIDC

The GitHub Actions workflow requires federated identity credentials to authenticate with Azure using OIDC. This is more secure than using service principal secrets.

### 1.1 Set Variables
```bash
# Replace these values with your actual information
GITHUB_ORG_USER="YOUR_GITHUB_USERNAME_OR_ORG"
GITHUB_REPO="YOUR_REPO_NAME"
APP_REGISTRATION_ID="id-3rbky3yz3zumq"  # Your existing App Registration
```

### 1.2 Create Federated Identity Credential for Main Branch
```bash
az ad app federated-credential create \
  --id $APP_REGISTRATION_ID \
  --parameters '{
    "name": "github-actions-main",
    "issuer": "https://token.actions.githubusercontent.com",
    "subject": "repo:'"$GITHUB_ORG_USER"'/'"$GITHUB_REPO"':ref:refs/heads/main",
    "description": "GitHub Actions Main Branch",
    "audiences": ["api://AzureADTokenExchange"]
  }'
```

### 1.3 Create Federated Identity Credential for Pull Requests (Optional)
```bash
az ad app federated-credential create \
  --id $APP_REGISTRATION_ID \
  --parameters '{
    "name": "github-actions-pr",
    "issuer": "https://token.actions.githubusercontent.com",
    "subject": "repo:'"$GITHUB_ORG_USER"'/'"$GITHUB_REPO"':pull_request",
    "description": "GitHub Actions Pull Requests",
    "audiences": ["api://AzureADTokenExchange"]
  }'
```

### 1.4 Verify Role Assignments
Ensure your App Registration has the necessary permissions:
```bash
# Get the object ID of your App Registration
OBJECT_ID=$(az ad app show --id $APP_REGISTRATION_ID --query id -o tsv)

# List current role assignments
az role assignment list --assignee $OBJECT_ID --output table

# If needed, assign required roles (replace SUBSCRIPTION_ID and RESOURCE_GROUP with your values)
SUBSCRIPTION_ID="YOUR_SUBSCRIPTION_ID"
RESOURCE_GROUP="rg-crtwriter"

# Contributor role for resource management
az role assignment create \
  --assignee $OBJECT_ID \
  --role "Contributor" \
  --scope "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP"

# AcrPush role for container registry
az role assignment create \
  --assignee $OBJECT_ID \
  --role "AcrPush" \
  --scope "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.ContainerRegistry/registries/cr3rbky3yz3zumq"
```

## Step 2: Configure GitHub Secrets

Set the following secrets in your GitHub repository (Settings → Secrets and variables → Actions):

### Required Secrets:

1. **AZURE_CLIENT_ID**: `id-3rbky3yz3zumq`
2. **AZURE_TENANT_ID**: Your Azure tenant ID
3. **AZURE_SUBSCRIPTION_ID**: Your Azure subscription ID

### Using GitHub CLI:

```bash
# Set the secrets using GitHub CLI (install gh CLI first)
gh secret set AZURE_CLIENT_ID --body "id-3rbky3yz3zumq"
gh secret set AZURE_TENANT_ID --body "YOUR_TENANT_ID"
gh secret set AZURE_SUBSCRIPTION_ID --body "YOUR_SUBSCRIPTION_ID"
```

### Using GitHub Web Interface:

1. Go to your repository on GitHub
2. Navigate to Settings → Secrets and variables → Actions
3. Click "New repository secret" for each secret
4. Add the name and value for each secret

## Step 3: Get Required Azure IDs

If you need to find your Azure tenant ID and subscription ID:

```bash
# Get current subscription and tenant info
az account show --query '{subscriptionId:id, tenantId:tenantId}' -o table

# List all accessible subscriptions
az account list --query '[].{Name:name, SubscriptionId:id, TenantId:tenantId}' -o table
```

## Step 4: Test the Setup

After completing the above steps:

1. Push a commit to the main branch or create a pull request
2. Go to the Actions tab in your GitHub repository
3. Check if the workflow runs successfully
4. Monitor the deployment logs for any issues

## Troubleshooting

### Common Issues:

1. **"No federated identity credentials found"**: Ensure step 1.2 was completed successfully
2. **"Insufficient permissions"**: Check role assignments in step 1.4
3. **"Invalid audience"**: Ensure the audience is set to `["api://AzureADTokenExchange"]`
4. **"Subject mismatch"**: Verify the subject format matches your repository and branch

### Verify Federated Credentials:

```bash
# List federated identity credentials
az ad app federated-credential list --id $APP_REGISTRATION_ID -o table
```

### Debug Authentication:

Add this step to your workflow temporarily to debug authentication issues:

```yaml
- name: Debug Azure Context
  run: |
    az account show
    az group list --query '[].{Name:name, Location:location}' -o table
```

## Workflow Features

The deployment workflow includes:

- **Multi-stage Docker builds** with caching for faster builds
- **Automatic image tagging** with build number, latest, and commit SHA
- **Smart deployment** - creates new container apps or updates existing ones
- **Health checks** to verify successful deployments
- **Comprehensive logging** and deployment summaries
- **Resource optimization** with appropriate CPU/memory limits
- **Auto-scaling** configuration (1-10 replicas for API, 1-5 for web)

## Security Benefits of OIDC

Using OIDC instead of service principal secrets provides:

1. **No long-lived secrets** - Tokens are short-lived and automatically rotated
2. **Better security** - No credentials stored in GitHub secrets
3. **Audit trail** - Better tracking of authentication events
4. **Compliance** - Meets modern security compliance requirements

## Additional Resources

- [GitHub Actions OIDC Documentation](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect)
- [Azure Workload Identity Federation](https://docs.microsoft.com/en-us/azure/active-directory/develop/workload-identity-federation)
- [Azure Container Apps Documentation](https://docs.microsoft.com/en-us/azure/container-apps/)
