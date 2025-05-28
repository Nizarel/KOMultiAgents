# GitHub Actions Deployment Setup Guide

This guide helps you set up the required Azure credentials and permissions for the GitHub Actions workflow to deploy your applications to Azure Container Apps.

## Prerequisites

1. Azure subscription with access to:
   - Azure Container Registry: `cr3rbky3yz3zumq`
   - Azure Container Apps Environment: `agent-ca-env`
   - Resource Group: `rg-crtwriter`

2. GitHub repository with Actions enabled

## Step 1: Create Azure Service Principal

You need to create a service principal with the necessary permissions to build, push images, and deploy container apps.

### 1.1 Create Service Principal

```bash
# Get your subscription ID
az account show --query id -o tsv

# Create service principal with Contributor role
az ad sp create-for-rbac \
  --name "github-actions-container-deployment" \
  --role "Contributor" \
  --scopes "/subscriptions/{subscription-id}/resourceGroups/rg-crtwriter" \
  --sdk-auth
```

### 1.2 Additional Role Assignments

The service principal needs additional permissions:

```bash
# Get the service principal object ID
SP_ID=$(az ad sp list --display-name "github-actions-container-deployment" --query [0].id -o tsv)

# Assign AcrPush role for container registry
az role assignment create \
  --assignee $SP_ID \
  --role "AcrPush" \
  --scope "/subscriptions/{subscription-id}/resourceGroups/rg-crtwriter/providers/Microsoft.ContainerRegistry/registries/cr3rbky3yz3zumq"

# Assign Container Apps Contributor role
az role assignment create \
  --assignee $SP_ID \
  --role "Container Apps Contributor" \
  --scope "/subscriptions/{subscription-id}/resourceGroups/rg-crtwriter"
```

## Step 2: Configure GitHub Secrets

Add the following secret to your GitHub repository:

### 2.1 AZURE_CREDENTIALS

The output from the `az ad sp create-for-rbac` command should look like this:

```json
{
  "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "clientSecret": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

Add this entire JSON object as a secret named `AZURE_CREDENTIALS` in your GitHub repository:

1. Go to your GitHub repository
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Name: `AZURE_CREDENTIALS`
5. Value: Paste the entire JSON output from the service principal creation

### 2.2 Using GitHub CLI (Alternative)

If you have GitHub CLI installed:

```bash
# Set the AZURE_CREDENTIALS secret
gh secret set AZURE_CREDENTIALS --body '{
  "clientId": "your-client-id",
  "clientSecret": "your-client-secret", 
  "subscriptionId": "your-subscription-id",
  "tenantId": "your-tenant-id"
}'
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
