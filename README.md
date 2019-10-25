# Zero to JupyterHub with Azure Kubernetes Service

This repository is meant to be used in addition to the original Zero to JupyterHub K8s [repo](https://github.com/jupyterhub/zero-to-jupyterhub-k8s), for Azure Kubernetes service (AKS).

# Pre-requsites

* Azure subscription
* [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) > 2.0.75
* [kubectl] (https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [Helm](https://zero-to-jupyterhub.readthedocs.io/en/latest/setup-helm.html) > 2.11 

# Creating the AKS Cluster

This is meant to be supplementary to the documenation [here](https://zero-to-jupyterhub.readthedocs.io/en/latest/microsoft/step-zero-azure-autoscale.html) that describes deploying an AKS cluster. The only difference is that with the commands below, both the Multiple Node Pools and the Cluster Autoscaler features will be enabled on the cluster. Please note that both of these features are still in preview:

    * [Multiple Node Pools] (https://docs.microsoft.com/en-us/azure/aks/use-multiple-node-pools)
    * [Cluster Autoscaler] (https://docs.microsoft.com/en-us/azure/aks/cluster-autoscaler)


```bash
az account set -s <your-subscription-here>

az extension add --name aks-preview
az extension update --name aks-preview
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az provider register --namespace Microsoft.ContainerService

az aks create \
    --resource-group <your-resource-group> \
    --name <your-cluster-name> \
    --node-vm-size Standard_E8_v3 \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --enable-cluster-autoscaler \
    --kubernetes-version 1.13.10 \
    --load-balancer-sku standard \
    --min-count 2 \
    --max-count 3
```

# Deploying JupyterHub

Please follow the instructions listed [here](https://zero-to-jupyterhub.readthedocs.io/en/latest/setup-jupyterhub.html) using the `config.yaml` provided in the code-samples folder as a reference for JupyterHub deployed with the following additional features:

    * Azure Active Directory Integration
    * Secrets with Azure Key Vault
    * Azure Storage File Share 
    * Custom Docker Image (see code-samples folder)