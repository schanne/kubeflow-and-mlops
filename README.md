[![Build Status](https://dev.azure.com/sasrose/kubeflow/_apis/build/status/DivineOps.kubeflow-and-mlops?branchName=master)](https://dev.azure.com/sasrose/kubeflow/_build/latest?definitionId=84&branchName=master)
# MLOps with Kubeflow, Azure Machine Learning and Azure Pipelines

This repository provides a sample ML CI/CD pipeline using Kubeflow, Azure ML workspaces and Azure Pipelines

# Using this code

TODO

# Deploy Kubeflow

## Prerequisites

#### Install Azure CLI
[Install Azure CLI on Windows](
https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest) 

Install Azure CLI on a Mac:
```
brew update && brew install azure-cli
```
#### Install/Upgrade kubectl
```
az aks install-cli
```

## 1. Create the AKS cluster for Kubeflow

1. Login
```
az login
```

2. See available subscriptions 
```
az account list -o table
```

3. Choose the required subscription
```
az account set --subscription <name or id>
```

4. Get the location code for the required data center 
```
az account list-locations
```

5. Get the VM size code for the cluster VMs
```
az vm list-sizes --location <region_you_plan_to_use> -o table
```

Note: for this workload the VMs must use premium storage. It is alsonrecommended to use GPU enabled VMs. 

6. Create the resource group
```
az group create -n <resource_group_name> -l <location>
```

7. Create the Service Principal (SP)
```
az ad sp create-for-rbac 
```

The output of the above command will be similar to the below. Please note the GUIDs, as we will use them going forward

```
{
  "appId": "559513bd-0c19-4c1a-87cd-XXXXXXX",
  "displayName": "azure-cli-2019-03-04-21-35-28",
  "name": "http://azure-cli-2019-03-04-21-35-28",
  "password": "e763725a-5eee-40e8-a466-XXXXXX",
  "tenant": "72f988bf-86f1-41af-91ab-XXXXXXX"
}
```

8. Create the cluster
```
az aks create -g <resource_group_name> -n <cluster_name> -s <agent_vm_size> -c <agent_count> -l <location> --generate-ssh-keys --service-principal <appId> --client-secret <password>
```

