# GitHub Actions Deployment Setup Guide

This guide helps you set up the required Azure credentials and permissions for the GitHub Actions workflow to deploy your applications to Azure Container Apps using Managed Identity authentication.

## Prerequisites

1. Azure subscription with access to:
   - Azure Container Registry: `cr3rbky3yz3zumq`
   - Azure Container Apps Environment: `agent-ca-env`
   - Resource Group: `rg-crtwriter`

2. GitHub repository with Actions enabled

## Step 1: Create Azure App Registration for Managed Identity

You need to create an Azure App Registration with federated credentials for GitHub Actions.

### 1.1 Create App Registration

```bash
# Get your subscription ID
SUBSCRIPTION_ID=$(az account show --query id -o tsv)

# Create app registration
az ad app create --display-name "github-actions-container-deployment"

# Get the application ID
APP_ID=$(az ad app list --display-name "github-actions-container-deployment" --query [0].appId -o tsv)

# Create service principal
az ad sp create --id $APP_ID
```

### 1.2 Create Federated Credentials

```bash
# Create federated credential for main branch
az ad app federated-credential create \
  --id $APP_ID \
  --parameters '{
    "name": "github-main",
    "issuer": "https://token.actions.githubusercontent.com",
    "subject": "repo:YOUR_GITHUB_USERNAME/YOUR_REPO_NAME:ref:refs/heads/main",
    "audiences": ["api://AzureADTokenExchange"]
  }'

# Create federated credential for develop branch
az ad app federated-credential create \
  --id $APP_ID \
  --parameters '{
    "name": "github-develop", 
    "issuer": "https://token.actions.githubusercontent.com",
    "subject": "repo:YOUR_GITHUB_USERNAME/YOUR_REPO_NAME:ref:refs/heads/develop",
    "audiences": ["api://AzureADTokenExchange"]
  }'

# Create federated credential for pull requests
az ad app federated-credential create \
  --id $APP_ID \
  --parameters '{
    "name": "github-pr",
    "issuer": "https://token.actions.githubusercontent.com", 
    "subject": "repo:YOUR_GITHUB_USERNAME/YOUR_REPO_NAME:pull_request",
    "audiences": ["api://AzureADTokenExchange"]
  }'
```

### 1.3 Assign Required Roles

```bash
# Get the service principal object ID
SP_ID=$(az ad sp list --display-name "github-actions-container-deployment" --query [0].id -o tsv)

# Assign Contributor role to resource group
az role assignment create \
  --assignee $SP_ID \
  --role "Contributor" \
  --scope "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/rg-crtwriter"

# Assign AcrPush role for container registry
az role assignment create \
  --assignee $SP_ID \
  --role "AcrPush" \
  --scope "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/rg-crtwriter/providers/Microsoft.ContainerRegistry/registries/cr3rbky3yz3zumq"

# Assign Container Apps Contributor role
az role assignment create \
  --assignee $SP_ID \
  --role "Container Apps Contributor" \
  --scope "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/rg-crtwriter"
```

## Step 2: Configure GitHub Secrets

Add the following secrets to your GitHub repository:

### 2.1 Required Secrets

1. **AZURE_CLIENT_ID**: The Application (client) ID of your app registration
2. **AZURE_TENANT_ID**: Your Azure tenant ID  
3. **AZURE_SUBSCRIPTION_ID**: Your Azure subscription ID

Get these values:

```bash
# Get tenant ID
TENANT_ID=$(az account show --query tenantId -o tsv)

# Get subscription ID  
SUBSCRIPTION_ID=$(az account show --query id -o tsv)

# Get client ID (already stored in APP_ID variable)
echo "Client ID: $APP_ID"
echo "Tenant ID: $TENANT_ID" 
echo "Subscription ID: $SUBSCRIPTION_ID"
```

### 2.2 Using GitHub UI

1. Go to your GitHub repository
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret** for each:
   - Name: `AZURE_CLIENT_ID`, Value: `[your-app-id]`
   - Name: `AZURE_TENANT_ID`, Value: `[your-tenant-id]`
   - Name: `AZURE_SUBSCRIPTION_ID`, Value: `[your-subscription-id]`

### 2.3 Using GitHub CLI (Alternative)

```bash
# Set secrets using GitHub CLI
gh secret set AZURE_CLIENT_ID --body "$APP_ID"
gh secret set AZURE_TENANT_ID --body "$TENANT_ID"
gh secret set AZURE_SUBSCRIPTION_ID --body "$SUBSCRIPTION_ID"
```

## Step 3: Enable Container Registry System Identity

Ensure your Container Apps can pull from the registry using system-assigned managed identity:

```bash
# Enable system identity on the Container Apps Environment
az containerapp env identity assign \
  --name agent-ca-env \
  --resource-group rg-crtwriter \
  --system-assigned

# Get the environment's managed identity principal ID
PRINCIPAL_ID=$(az containerapp env show \
  --name agent-ca-env \
  --resource-group rg-crtwriter \
  --query identity.principalId -o tsv)

# Assign AcrPull permission to the managed identity
az role assignment create \
  --assignee $PRINCIPAL_ID \
  --role "AcrPull" \
  --scope "/subscriptions/{subscription-id}/resourceGroups/rg-crtwriter/providers/Microsoft.ContainerRegistry/registries/cr3rbky3yz3zumq"
```

## Step 4: Verify Setup

Test your setup by running this Azure CLI command:

```bash
# Test ACR access
az acr login --name cr3rbky3yz3zumq

# Test Container Apps access
az containerapp list --resource-group rg-crtwriter
```

## Step 5: Trigger Deployment

The workflow will trigger automatically when you:

1. Push to `main` or `develop` branches with changes in:
   - `src/api/**`
   - `src/web/**`
   - `.github/workflows/depworkflow.yml`

2. Create a pull request to `main` branch with changes in the above paths

3. Manually trigger via the GitHub Actions UI (workflow_dispatch)

## Workflow Features

The workflow includes:

- **Multi-stage Docker builds** with caching for faster builds
- **Automatic image tagging** with build number, latest, and commit SHA
- **Smart deployment** - creates new container apps or updates existing ones
- **Health checks** to verify successful deployments
- **Comprehensive logging** and deployment summaries
- **Resource optimization** with appropriate CPU/memory limits
- **Auto-scaling** configuration (1-10 replicas for API, 1-5 for web)

## Troubleshooting

### Common Issues

1. **Permission errors**: Ensure service principal has all required role assignments
2. **Registry access**: Verify ACR permissions and managed identity setup
3. **Container App creation fails**: Check that the environment exists and is healthy
4. **Health checks fail**: Verify your applications expose the expected endpoints

### Debug Commands

```bash
# Check service principal permissions
az role assignment list --assignee {service-principal-id}

# Check container app status
az containerapp show --name agent-api2 --resource-group rg-crtwriter

# Check container app logs
az containerapp logs show --name agent-api2 --resource-group rg-crtwriter

# Check container registry
az acr repository list --name cr3rbky3yz3zumq
```

## Security Best Practices

1. **Rotate secrets regularly** - Update service principal credentials periodically
2. **Use least privilege** - Grant only necessary permissions
3. **Monitor access** - Review Azure Activity logs for unusual activity
4. **Secure branches** - Protect main/develop branches with required reviews
5. **Environment separation** - Use different service principals for different environments

## Next Steps

After successful deployment, consider:

1. Setting up **monitoring and alerting** with Azure Application Insights
2. Implementing **blue-green deployments** for zero-downtime updates
3. Adding **integration tests** to the workflow
4. Setting up **multiple environments** (dev, staging, prod)
5. Configuring **custom domains** and SSL certificates
