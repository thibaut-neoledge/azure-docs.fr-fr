---
title: "Déployez une application sur un groupe de machines virtuelles identiques Azure | Microsoft Docs"
description: "Apprenez à déployer une application de mise à l’échelle automatique simple sur un jeu de mise à l’échelle de machines virtuelles identiques à l’aide d’un modèle Azure Resource Manager."
services: virtual-machine-scale-sets
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/4/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 8a903cd870f01f9ca6224efd1386b68c63e3aa98
ms.contentlocale: fr-fr
ms.lasthandoff: 04/06/2017


---

# <a name="deploy-an-autoscaling-app-using-a-template"></a>Déploiement d’une application de mise à l’échelle à l’aide d’un modèle

Les [modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) constituent un excellent moyen de déployer des groupes de ressources liées. Ce didacticiel se base sur [Déploiement d’un jeu de mise à l’échelle simple](virtual-machine-scale-sets-mvss-start.md) et décrit comment déployer une application de mise à l’échelle automatique simple sur un jeu de mise à l’échelle à l’aide d’un modèle Azure Resource Manager.  Vous pouvez également configurer la mise à l’échelle automatique en utilisant PowerShell, CLI ou le portail. Pour plus d’informations, consultez [Vue d’ensemble de la mise à l’échelle automatique](virtual-machine-scale-sets-autoscale-overview.md).

## <a name="two-quickstart-templates"></a>Deux modèles de démarrage rapide
Lorsque vous déployez un jeu de mise à l’échelle, vous pouvez installer de nouveaux logiciels sur une image de plateforme à l’aide d’une [extension de machine virtuelle](../virtual-machines/virtual-machines-windows-extensions-features.md). Les extensions de machine virtuelle sont de petites applications permettant d’exécuter des tâches de configuration et d’automatisation post-déploiement sur des machines virtuelles Azure, comme le déploiement d’une application. Deux modèles différents sont fournis dans [Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) et montrent comment déployer une application de mise à l’échelle automatique sur un jeu de mise à l’échelle à l’aide des extensions de machine virtuelle.

### <a name="python-http-server-on-linux"></a>Serveur HTTP Python sur Linux
Le modèle [serveur HTTP Python sur Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) déploie une application simple de mise à l’échelle automatique simple sur un jeu de mise à l’échelle Linux.  [Bottle](http://bottlepy.org/docs/dev/), un framework web de Python et un serveur HTTP simple sont déployés sur chaque machine virtuelle dans le jeu de mise à l’échelle à l’aide d’une extension de machine virtuelle à script personnalisé. Le jeu de mise à l’échelle monte en puissance lorsque l’utilisation moyenne de l’UC entre toutes les machines virtuelles est supérieure à 60 % et descend en puissance lorsque l’utilisation moyenne est inférieure à 30 %.

En plus de la ressource de jeu de mise à l’échelle, l’exemple de modèle *azuredeploy.json* déclare également le réseau virtuel, l’adresse IP publique, l’équilibrage de charge et les paramètres de mise à l’échelle automatique.  Pour plus d’informations sur la création de ces ressources dans un modèle, consultez la page [Jeu de mise à l’échelle Linux avec mise à l’échelle automatique](virtual-machine-scale-sets-linux-autoscale.md).

Dans le modèle *azuredeploy.json*, la propriété `extensionProfile` de la ressource `Microsoft.Compute/virtualMachineScaleSets` spécifie une extension à script personnalisé. `fileUris` spécifie l’emplacement du ou des scripts. Dans ce cas, nous avons deux fichiers : *workserver.py*, qui définit un serveur HTTP simple, et *installserver.sh*, qui installe Bottle et démarre le serveur HTTP. `commandToExecute` spécifie la commande à exécuter une fois le jeu de mise à l’échelle déployé.

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "lapextension",
                "properties": {
                  "publisher": "Microsoft.Azure.Extensions",
                  "type": "CustomScript",
                  "typeHandlerVersion": "2.0",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "fileUris": [
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
                    ],
                    "commandToExecute": "bash installserver.sh"
                  }
                }
              }
            ]
          }
```

### <a name="aspnet-mvc-application-on-windows"></a>Application ASP.NET MVC sur Windows
L’exemple de modèle [Application ASP.NET MVC sur Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) déploie une application ASP.NET MVC simple dans IIS sur un jeu de mise à l’échelle Windows.  IIS et l’application MVC sont déployés à l’aide de l’extension de machine virtuelle [Configuration d’état souhaité (DSC) PowerShell](virtual-machine-scale-sets-dsc.md).  Le jeu de mise à l’échelle monte en puissance (sur une instance de machine virtuelle à la fois) lorsque l’utilisation de l’UC dépasse 50 % pendant 5 minutes. 

En plus de la ressource de jeu de mise à l’échelle, l’exemple de modèle *azuredeploy.json* déclare également le réseau virtuel, l’adresse IP publique, l’équilibrage de charge et les paramètres de mise à l’échelle automatique. Ce modèle illustre également la mise à niveau de l’application.  Pour plus d’informations sur la création de ces ressources dans un modèle, consultez la page [Jeu de mise à l’échelle Windows avec mise à l’échelle automatique](virtual-machine-scale-sets-windows-autoscale.md).

Dans le modèle *azuredeploy.json*, la propriété `extensionProfile` de la ressource `Microsoft.Compute/virtualMachineScaleSets` spécifie une extension [Configuration d’état souhaité (DSC)](virtual-machine-scale-sets-dsc.md) qui installe IIS et une application par défaut à partir d’un package WebDeploy.  Le script *IISInstall.ps1* installe IIS sur la machine virtuelle et se trouve dans le dossier *DSC*.  L’application web MVC se trouve dans le dossier *WebDeploy*.  Les chemins d’accès pour le script d’installation et de l’application web sont définis dans les paramètres `powershelldscZip` et `webDeployPackage` dans le fichier *azuredeploy.parameters.json*. 

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Powershell.DSC",
                "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.9",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
```

## <a name="deploy-the-template"></a>Déployer le modèle
Le moyen le plus simple pour déployer le modèle [serveur HTTP Python sur Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) ou [application ASP.NET MVC sur Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) consiste à utiliser le bouton **Déployer dans Azure** qui se trouve dans les fichiers Lisez-moi dans GitHub.  Vous pouvez également utiliser PowerShell ou l’interface Azure CLI pour déployer les exemples de modèles.

### <a name="powershell"></a>PowerShell
Copiez les fichiers de [serveur HTTP Python sur Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) ou [application ASP.NET MVC sur Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) du référentiel GitHub dans un dossier sur votre ordinateur local.  Ouvrez le fichier *azuredeploy.parameters.json* et de mettez à jour les valeurs par défaut des paramètres `vmssName`, `adminUsername` et `adminPassword`. Enregistrez le script PowerShell suivant dans *deploy.ps1* dans le même dossier d’exemple que le modèle *azuredeploy.json* modèle. Pour déployer l’exemple de modèle, exécutez le script *deploy.ps1* à partir d’une fenêtre de commande PowerShell.

```powershell
param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "azuredeploy.json",

 [string]
 $parametersFilePath = "azuredeploy.parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @("microsoft.resources");
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

