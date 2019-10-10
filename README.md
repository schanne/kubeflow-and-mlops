[![Build Status](https://dev.azure.com/sasrose/kubeflow/_apis/build/status/DivineOps.kubeflow-and-mlops?branchName=master)](https://dev.azure.com/sasrose/kubeflow/_build/latest?definitionId=84&branchName=master)
# MLOps with Kubeflow, Azure Machine Learning and Azure Pipelines

This repository provides a sample ML CI/CD pipeline using Kubeflow, Azure ML workspaces and Azure Pipelines

# Using this code

TODO

# Deploy Kubeflow

Note: this guide deploys Kubeflow on AKS on Azure platform. For more examples, and guides for other platforms, please refer to the [kubeflow.org getting started page](https://www.kubeflow.org/docs/started/getting-started/)

## Prerequisites

#### Install Azure CLI
[Install Azure CLI on Windows machine](
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

Note: if you already have an AKS cluster, you can skip to the step 1.9.

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

9. Connect to the AKS cluster you've deployed
```
az aks get-credentials -n <cluster_name> -g <resource_group_name>
```

10. Sanity check that all the base namespaces are running
```
kubectl get all --all-namespaces 
```

## 2. Install Kubeflow

For more examples, and guides for other platforms, please refer to the [kubeflow.org getting started page](https://www.kubeflow.org/docs/started/getting-started/)
1. Download the latest kfctl executable from the [kubeflow releases page](https://github.com/kubeflow/kubeflow/releases/) to your local machine

#### On a Mac
Use the latest Mac executable, e.g. 
https://github.com/kubeflow/kubeflow/releases/download/v0.6.2/kfctl_v0.6.2_darwin.tar.gz

#### On a Windows machine
Use the [Linux Subsystem](https://docs.microsoft.com/en-us/windows/wsl/install-win10) or the [Azure Cloud Shell](http://shell.azure.com) bash environment.

Use the latest Linux executable, e.g. https://github.com/kubeflow/kubeflow/releases/download/v0.6.2/kfctl_v0.6.2_linux.tar.gz

2. Choose the desired location, and unpack the tar.gz
```
tar -xvf kfctl_<release tag>_<platform>.tar.gz
```

3. Add the kfctl location to PATH
```
export PATH=$PATH:<path to where kfctl was unpacked>
```

4. Choose and set the directory name for kubeflow setup files
```
export KFAPP=<your choice of application directory name> (ensure the name is lowercase)
```

5. Choose the latest istio config file 
```
export CONFIG=<file path or url to config to use>
```
e.g.
```
export CONFIG=https://raw.githubusercontent.com/kubeflow/kubeflow/v0.6.2/bootstrap/config/kfctl_k8s_istio.0.6.2.yaml
````

6. Generate the configuration files and deploy kubeflow

Note: make sure that you are connected to the cluster. Refer to step 1.9. for details. 

```
cd ${KFAPP}
kfctl generate all -V
kfctl apply all -V
```