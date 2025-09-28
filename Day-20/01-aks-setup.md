# AKS setup using CLI

## Create Azure Resource Group

```
az group create --name keyvault-demo --location eastus
```

## AKS Creation and Configuration

### Create an AKS cluster with Azure Key Vault provider for Secrets Store CSI Driver support

```
az aks create \
  --name keyvault-demo-cluster \
  --resource-group keyvault-demo \
  --node-count 1 \
  --node-vm-size Standard_B2s \
  --enable-addons azure-keyvault-secrets-provider \
  --enable-oidc-issuer \
  --enable-workload-identity
```

### Get the Kubernetes cluster credentials (Update kubeconfig)

```
az aks get-credentials --resource-group keyvault-demo --name keyvault-demo-cluster
```

#### Verify that each node in your cluster's node pool has a Secrets Store CSI Driver pod and a Secrets Store Provider Azure pod running

```
kubectl get pods -n kube-system -l 'app in (secrets-store-csi-driver,secrets-store-provider-azure)' -o wide
```

## Registration with Microsoft.KeyVault Resource Provider

- Your subscription isn’t yet registered for the Microsoft.KeyVault resource provider. Before you can create a Key Vault, you need to register it.

Run this command:

```
az provider register --namespace Microsoft.KeyVault
```

Then check the registration status:

```
az provider show --namespace Microsoft.KeyVault -o table
```

Once it’s registered. move to the next step.

## Keyvault creation and configuration

- Create a key vault with Azure role-based access control (Azure RBAC).

```
az keyvault create -n aks-demo-abdullah -g keyvault-demo -l eastus --enable-rbac-authorization
```
