---
title: "Connecter des machines virtuelles Azure à Log Analytics | Microsoft Docs"
description: "Pour les machines virtuelles Windows et Linux s’exécutant dans Azure, il est recommandé de collecter les journaux et les métriques en installant l’extension de machine virtuelle Azure Log Analytics. Vous pouvez utiliser le portail Azure ou PowerShell pour installer l’extension de machine virtuelle Log Analytics sur des machines virtuelles Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: ewinner
editor: 
ms.assetid: ca39e586-a6af-42fe-862e-80978a58d9b1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: richrund
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: cdae291b546fef4d7fdb8b067c8e4f4c9708d43f
ms.contentlocale: fr-fr
ms.lasthandoff: 08/11/2017

---
# <a name="connect-azure-virtual-machines-to-log-analytics-with-a-log-analytics-agent"></a>Connecter des machines virtuelles Azure à Log Analytics avec un agent Log Analytics

Pour les ordinateurs Windows et Linux, la méthode recommandée pour collecter des journaux et des métriques est d’installer l’agent Log Analytics.

Le moyen le plus simple pour installer l’agent de Log Analytics sur des machines virtuelles Azure consiste à utiliser l’extension de machine virtuelle Log Analytics.  L’utilisation de l’extension simplifie le processus d’installation et configure automatiquement l’agent pour qu’il envoie des données à l’espace de travail Log Analytics que vous spécifiez. L’agent est également mis à niveau automatiquement, de façon à ce que vous disposiez des fonctionnalités et correctifs les plus récents.

Pour des machines virtuelles Windows, vous activez l’extension de machine virtuelle *Microsoft Monitoring Agent*.
Pour des machines virtuelles Linux, vous activez l’extension de machine virtuelle *Agent OMS pour Linux*.

En savoir plus sur les [extensions de machine virtuelle Azure](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) et l’[agent Linux](../virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Lorsque vous utilisez une collecte basée sur agent pour les données de journal, vous devez configurer des [sources de données dans Log Analytics](log-analytics-data-sources.md) afin de spécifier les journaux et les métriques à collecter.

> [!IMPORTANT]
> Si vous configurez Log Analytics pour indexer les données de journal à l’aide des [diagnostics Azure](log-analytics-azure-storage.md), et configurez l’agent pour collecter les mêmes journaux, les journaux sont collectés deux fois. Vous êtes facturé pour les deux sources de données. Si l’agent est installé, collectez les données de journal uniquement à l’aide de l’agent. Ne configurez pas Log Analytics pour collecter les données de journal à partir des diagnostics Azure.
>
>

Il existe trois méthodes simples pour activer l’extension de machine virtuelle Log Analytics :

* En utilisant le portail Azure
* En utilisant Azure PowerShell
* En utilisant un modèle Azure Resource Manager

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>Activer l’extension de machine virtuelle dans le portail Azure
Vous pouvez installer l’agent pour Log Analytics et connecter la machine virtuelle Azure sur laquelle il s’exécute via le [portail Azure](https://portal.azure.com).

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>Pour installer l’agent Log Analytics et connecter la machine virtuelle à un espace de travail Log Analytics
1. Connectez-vous au [portail Azure](http://portal.azure.com).
2. Sélectionnez **Parcourir** sur le côté gauche du portail, puis accédez à **Log Analytics (OMS)** et sélectionnez-le.
3. Dans la liste des espaces de travail Log Analytics, sélectionnez celui que vous voulez utiliser avec la machine virtuelle Azure.  
   ![Espaces de travail OMS](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4. Sous **Gestion de Log Analytics**, sélectionnez **Machines virtuelles**.  
   ![Machines virtuelles](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5. Dans la liste des **Machines virtuelles**, sélectionnez celle sur laquelle vous voulez installer l’agent. L’**État de la connexion OMS** pour la machine virtuelle indique que celle-ci est **Non connectée**.  
   ![Machine virtuelle non connectée](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6. Dans les détails de votre machine virtuelle, sélectionnez **Connecter**. L’agent est automatiquement installé et configuré pour votre espace de travail Log Analytics. Ce processus prend quelques minutes durant lesquelles l’état de la connexion OMS est *Connexion en cours...*.  
   ![Connecter une machine virtuelle](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7. Une fois l’agent installé et connecté, l’état de la **Connexion OMS** est mis à jour et affiche **Cet espace de travail**.  
   ![Connecté](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)

## <a name="enable-the-vm-extension-using-powershell"></a>Activer l’extension de machine virtuelle à l’aide de PowerShell
Lorsque vous configurez votre machine virtuelle à l’aide de PowerShell, vous devez fournir **l’ID d’espace de travail** et **workspaceKey**. Les noms de propriété dans votre configuration json respectent **la casse**.

Vous pouvez trouver L’ID et la clé dans la page **Paramètres** du portail OMS, ou en utilisant PowerShell comme indiqué dans l’exemple précédent.

![ID d’espace de travail et clé primaire](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

Il existe différentes commandes pour les machines virtuelles Azure Classic et Azure Resource Manager. Vous trouverez ci-dessous des exemples pour les machines virtuelles Azure Classic et Azure Resource Manager.

Pour les machines virtuelles Azure Classic, utilisez l’exemple PowerShell suivant :

```PowerShell
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Pour les machines virtuelles Linux Resource Manager utilisant l’interface de ligne de commande suivante
```azurecli
az vm extension set --resource-group myRGMonitor --vm-name myMonitorVM --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --version 1.3 --protected-settings ‘{"workspaceKey": "<workspace-key>"}’ --settings ‘{"workspaceId": "<workspace-id>"}’ 
```

Pour les machines virtuelles Azure Resource Manager, utilisez l’exemple PowerShell suivant :

```PowerShell
Login-AzureRMAccount
Select-AzureRMSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```


## <a name="deploy-the-vm-extension-using-a-template"></a>Déployer l’extension de machine virtuelle à l’aide d’un modèle
Azure Resource Manager vous permet de créer un modèle (au format JSON) définissant le déploiement et la configuration de votre application. Ce modèle est connu sous le nom de modèle Resource Manager et permet de définir le déploiement de façon déclarative. En utilisant un modèle, vous pouvez déployer votre application à plusieurs reprises tout au long de son cycle de vie, et avoir ainsi l’assurance que vos ressources sont déployées dans un état cohérent.

En incluant l’agent Log Analytics dans votre modèle Resource Manager, vous êtes certain que chaque machine virtuelle est préconfigurée pour envoyer des rapports à votre espace de travail Log Analytics.

Pour plus d’informations sur les modèles Azure Resource Manager, voir [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Voici un exemple de modèle Azure Resource Manager utilisé pour déployer une machine virtuelle exécutant Windows avec l’extension Microsoft Monitoring Agent installée. Il s’agit d’un modèle de machine virtuelle typique, avec les ajouts suivants :

* Paramètres workspaceId et workspaceName
* Section d’extension de ressources Microsoft.EnterpriseCloud.Monitoring
* Sorties pour rechercher workspaceId et workspaceSharedKey

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMS workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
          "computername": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

Vous pouvez déployer un modèle à l’aide de la commande PowerShell suivante :

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-the-log-analytics-vm-extension"></a>Dépannage de l’extension de machine virtuelle Log Analytics
Vous recevez généralement un message émis par le portail Azure ou par Azure PowerShell en cas de problème.

1. Connectez-vous au [portail Azure](http://portal.azure.com).
2. Recherchez la machine virtuelle et accédez aux détails de cette dernière.
3. Cliquez sur **Extensions** pour vérifier si l’extension d’OMS est activée.

   ![Vue Extension de machine virtuelle](./media/log-analytics-azure-vm-extension/oms-vmview-extensions.png)

4. Cliquez sur l’extension *MicrosoftMonitoringAgent*(Windows) ou *OmsAgentForLinux*(Linux) et consultez les détails. 

   ![Détails de l’extension de machine virtuelle](./media/log-analytics-azure-vm-extension/oms-vmview-extensiondetails.png)

### <a name="troubleshooting-windows-virtual-machines"></a>Résolution des problèmes de machines virtuelles
Si l’extension d’agent de machine virtuelle *Microsoft Monitoring Agent* ne s’installe pas ou ne génère pas de rapports, vous pouvez procéder comme suit pour résoudre le problème.

1. Vérifiez si l’agent de machine virtuelle Azure est installé et fonctionne correctement en suivant la procédure décrite dans l’article [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Vous pouvez également consulter le fichier journal de l’agent de machine virtuelle `C:\WindowsAzure\logs\WaAppAgent.log`.
   * Si le journal n’existe pas, l’agent de machine virtuelle n’est pas installé.
     * [Installez l’agent de machine virtuelle Azure sur des machines virtuelles classiques](../virtual-machines/windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
2. Vérifiez que la tâche de vérification des pulsations de l’extension Microsoft Monitoring Agent est en cours d’exécution en procédant comme suit :
   * Connectez-vous à la machine virtuelle.
   * Ouvrez le Planificateur de tâches et recherchez la tâche `update_azureoperationalinsight_agent_heartbeat`.
   * Vérifiez que la tâche est activée et exécutée toutes les minutes.
   * Consultez le fichier journal des pulsations dans `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`.
3. Examinez les fichiers de journaux d’extension de machine virtuelle Microsoft Monitoring Agent dans `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`.
4. Vérifiez que la machine virtuelle peut exécuter des scripts PowerShell.
5. Vérifiez que les autorisations sur C:\Windows\temp n’ont pas été modifiées.
6. Affichez l’état de Microsoft Monitoring Agent en tapant la commande suivante dans une fenêtre PowerShell avec des privilèges élevés sur la machine virtuelle `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`.
7. Examinez les fichiers journaux d’installation de Microsoft Monitoring Agent dans `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`.

Pour plus d’informations, consultez [Résolution des problèmes des extensions Windows](../virtual-machines/windows/extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="troubleshooting-linux-virtual-machines"></a>Résolution des problèmes des machines virtuelles Linux
Si l’extension d’agent de machine virtuelle *Agent OMS pour Linux* ne s’installe pas ou ne génère pas de rapports, vous pouvez procéder comme suit pour résoudre le problème.

1. Si l’état de l’extension est *Inconnu*, vérifiez si l’agent de machine virtuelle Azure est installé et fonctionne correctement en examinant le fichier journal de l’agent de machine virtuelle `/var/log/waagent.log`
   * Si le journal n’existe pas, l’agent de machine virtuelle n’est pas installé.
   * [Installez l’agent de machine virtuelle Azure sur les machines virtuelles Linux](../virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
2. Pour d’autres états défectueux, examinez les fichiers journaux de l’extension de machine virtuelle Agent OMS pour Linux dans `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` et `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`.
3. Si l’état de l’extension est intègre, mais que les données ne sont pas chargées, examinez les fichiers journaux de l’Agent OMS pour Linux dans `/var/opt/microsoft/omsagent/log/omsagent.log`.

## <a name="next-steps"></a>Étapes suivantes
* Configurez les [sources de données dans Log Analytics](log-analytics-data-sources.md) pour spécifier les journaux et les métriques à collecter.
* Pour collecter des données à partir de machines virtuelles, [ajoutez des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).
* Pour d’autres ressources s’exécutant dans Azure, [collectez des données à l’aide des diagnostics Azure](log-analytics-azure-storage.md).

Pour des ordinateurs qui ne sont pas dans Azure, vous pouvez installer l’agent Log Analytics à l’aide des méthodes décrites dans les articles suivants :

* [Connecter des ordinateurs Windows à Log Analytics](log-analytics-windows-agents.md)
* [Connecter des ordinateurs Linux à Log Analytics](log-analytics-linux-agents.md)

