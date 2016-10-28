# mbrace-arm
Experimental repository containing an ARM template for deploying MBrace clusters to Azure

## Deploy a cluster from your web browser now

[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?repository=https://github.com/isaacabraham/mbrace-arm)

To quickly deploy a cluster from your web browser, click the Deploy to Azure button above, which will then guide you through the process of deploying your MBrace cluster.

## How to use
1. Open up a Powershell window.
2. CD to this directory.
3. Execute the following commands:

```powershell
## Logs into Azure using your credentials - no need for pub setting 
Login-AzureRmAccount

## Create a "resource group" for the cluster, called MyCluster
New-AzureRmResourceGroup -Name MyCluster -Location "North Europe"

## Create a 'small' MBrace cluster called MyCluster!
New-AzureRmResourceGroupDeployment -ResourceGroupName MyCluster -TemplateFile .\azuredeploy.json -TemplateParameterFile "small-cluster.json"
```

You will notice the output of this operation will contain the Service Bus and Storage Account keys - these can be used to connect to the cluster. You can also easily fine tune the process as follows:

```powershell
## Optional, if you have multiple subscriptions
Set-AzureRmContext -SubscriptionName "My Subscription"

## Resize the cluster to a 'medium' sized cluster (2 nodes)
New-AzureRmResourceGroupDeployment -ResourceGroupName MyCluster -TemplateFile .\azuredeploy.json -TemplateParameterFile "medium-cluster.json"

## Manually configure the cluster size to 8 large nodes (S3 = large)
New-AzureRmResourceGroupDeployment -ResourceGroupName MyCluster -TemplateFile .\azuredeploy.json -clusterSize 8 -nodeSize "S3"

## Destroy the cluster
Remove-AzureRmResourceGroup -Name "MyCluster"
```

## Benefits over Cloud Service deployment
* Rapid provisioning / deprovisioning
* Code-free - no requirements for MBrace to use MAML to provision a cluster
* Simpler model for cluster provisioning with ready-made "small" "medium" and "large" cluster parameter files.
* Built in Azure diagnostics / charting within the Portal
* Fully supported by Microsoft going forwards
* Easier management - a single MBrace Worker executable / package that is accessible over HTTP can function across
all node sizes, unlike Cloud Services. Packages are simple zip files - no need to create cspackages.
* Easier deployment - no need to manually copy / deploy the MBrace application - handled for us by ARM.

## Restrictions
* Requires PowerShell currently. This could be removed using the .NET ARM SDK
* As this implementation of MBrace runs in a web job in the Azure App Service, it does not have admin privileges. Therefore, it cannot retrieve PerfMon
counters for the purposes of metrics e.g. ``ShowWorkers()``.
* Limited cluster and node sizes - maximum of 20 Extra Large nodes.
