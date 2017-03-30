---
title: "Déployer des ressources avec le modèle et PowerShell | Microsoft Docs"
description: "Utilisez Azure Resource Manager et Azure PowerShell pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: ea42f634fabbbaf13f0d97c71a9cb8d1d8102dea
ms.lasthandoff: 03/16/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Interface de ligne de commande Azure](resource-group-template-deploy-cli.md)
> * [Portail](resource-group-template-deploy-portal.md)
> * [API REST](resource-group-template-deploy-rest.md)
> 
> 

Cette rubrique explique comment utiliser Azure PowerShell avec les modèles Resource Manager pour déployer vos ressources dans Azure. Votre modèle peut être un fichier local ou un fichier externe disponible par le biais d’un URI. Lorsque votre modèle se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle et fournir un jeton de signature d’accès partagé (SAP) au cours du déploiement.

## <a name="deploy"></a>Déployer
* Afin de prendre rapidement en main le déploiement, utilisez les commandes suivantes pour déployer un modèle local avec des paramètres inline :

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {your-subscription-ID}
  New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
  ```

  Le déploiement peut prendre plusieurs minutes. Au terme, vous voyez un message qui inclut le résultat :

  ```powershell
  ProvisioningState       : Succeeded
  ```

* L’applet de commande `Set-AzureRmContext` est nécessaire uniquement si vous souhaitez utiliser un abonnement autre que votre abonnement par défaut. Pour afficher tous vos abonnements et leurs numéros d’identification, utilisez :

  ```powershell
  Get-AzureRmSubscription
  ```

* Pour déployer un modèle externe, utilisez le paramètre **TemplateUri** :

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
  ```

* Pour passer les valeurs de paramètre dans un fichier, utilisez le paramètre **TemplateParameterFile** :

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json -TemplateParameterFile c:\MyTemplates\storage.parameters.json
  ```

  Le fichier de paramètres doit être au format suivant :

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
        "storageNamePrefix": {
            "value": "contoso"
        },
        "storageSKU": {
            "value": "Standard_GRS"
        }
     }
   }
   ```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Pour utiliser le mode complet, utilisez le paramètre **Mode** :

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```

## <a name="deploy-private-template-with-sas-token"></a>Déploiement d’un modèle privé avec un jeton SAP
Vous pouvez ajouter vos modèles à un compte de stockage et les lier au cours du déploiement avec un jeton SAP.

> [!IMPORTANT]
> Une fois les étapes ci-dessous suivies, l’objet blob contenant le modèle n’est accessible qu’au propriétaire du compte. Toutefois, lorsque vous créez un jeton SAP pour l’objet blob, celui-ci est accessible à toute personne ayant cet URI. Si un autre utilisateur intercepte l’URI, il pourra accéder au modèle. L’utilisation d’un jeton SAP est un bon moyen de limiter l’accès à vos modèles, mais vous ne devez pas inclure de données sensibles comme des mots de passe directement dans le modèle.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Ajouter un modèle privé au compte de stockage
L’exemple suivant configure un conteneur de compte de stockage privé et charge un modèle :
   
```powershell
New-AzureRmResourceGroup -Name ManageGroup -Location "South Central US"
New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}
New-AzureStorageContainer -Name templates -Permission Off
Set-AzureStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

### <a name="provide-sas-token-during-deployment"></a>Fournir un jeton SAP au cours du déploiement
Pour déployer un modèle dans un compte de stockage privé, générez un jeton SAP et incluez-le dans l’URI du modèle. Définissez le délai d’expiration de façon à laisser suffisamment de temps pour terminer le déploiement.
   
```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}
$templateuri = New-AzureStorageBlobSASToken -Container templates -Blob storage.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Pour accéder à un exemple d’utilisation d’un jeton SAP avec des modèles liés, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

## <a name="parameters"></a>parameters
   
Si votre modèle inclut un paramètre utilisant le même nom que l’un des paramètres dans la commande PowerShell, vous êtes invité à fournir une valeur pour ce paramètre. Azure PowerShell présente le paramètre de votre modèle en incluant le suffixe **FromTemplate**. Par exemple, un paramètre nommé **ResourceGroupName** dans votre modèle est en conflit avec le paramètre **ResourceGroupName** dans l’applet de commande [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment). Vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**. En général, vous devez éviter cette confusion en ne nommant pas les paramètres avec un nom identique à celui des paramètres utilisés pour les opérations de déploiement.

Vous pouvez utiliser des paramètres inline et un fichier de paramètres local pendant la même opération de déploiement. Par exemple, vous pouvez spécifier certaines valeurs dans le fichier de paramètres local et ajouter d’autres valeurs inline pendant le déploiement. Si vous fournissez des valeurs pour un paramètre à la fois dans le fichier de paramètres local et inline, la valeur inline est prioritaire.

Cependant, lorsque vous utilisez un fichier de paramètres externe, vous ne pouvez pas passer d’autres valeurs inline ou tirées d’un fichier local. Lorsque vous spécifiez un fichier de paramètres dans le paramètre **TemplateParameterUri**, tous les paramètres inline sont ignorés. Fournissez toutes les valeurs de paramètre dans le fichier externe. Si votre modèle inclut une valeur sensible que vous ne pouvez pas inclure dans le fichier de paramètres, ajoutez cette valeur dans un coffre de clés, ou fournissez de manière dynamique toutes des valeurs de paramètre inline.

## <a name="debug"></a>Déboguer

Si vous souhaitez consigner des informations supplémentaires sur le déploiement qui peuvent vous aider à résoudre des erreurs de déploiement, utilisez le paramètre **DeploymentDebugLogLevel**. Vous pouvez demander à ce que le contenu de la demande et/ou de la réponse soit consigné avec l’opération de déploiement.
   
```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleGroup -TemplateFile storage.json
```

Pour obtenir plus d’informations sur une opération de déploiement qui a échoué, utilisez :

```powershell
(Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="complete-deployment-script"></a>Exécuter le script de déploiement

L’exemple suivant montre le script PowerShell pour le déploiement d’un modèle qui est généré par la fonctionnalité [Exporter le modèle](resource-manager-export-template.md) :

```powershell
<#
 .SYNOPSIS
    Deploys a template to Azure

 .DESCRIPTION
    Deploys an Azure Resource Manager template

 .PARAMETER subscriptionId
    The subscription id where the template will be deployed.

 .PARAMETER resourceGroupName
    The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

 .PARAMETER resourceGroupLocation
    Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

 .PARAMETER deploymentName
    The deployment name.

 .PARAMETER templateFilePath
    Optional, path to the template file. Defaults to template.json.

 .PARAMETER parametersFilePath
    Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
#>

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
 $templateFilePath = "template.json",

 [string]
 $parametersFilePath = "parameters.json"
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
$resourceProviders = @();
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
* Pour découvrir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez [Déployer des ressources à l’aide de bibliothèques .NET et d’un modèle](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pour définir des paramètres dans le modèle, consultez [Création de modèles](resource-group-authoring-templates.md#parameters).
* Pour obtenir des instructions sur le déploiement de votre solution dans différents environnements, consultez [Environnements de développement et de test dans Microsoft Azure](solution-dev-test-environments.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).
* Pour consulter une série en quatre parties sur l’automatisation du déploiement, consultez [Automatisation des déploiements d’applications sur des machines virtuelles Azure](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cette série couvre l’architecture, l’accès, la sécurité, la disponibilité, la mise à l’échelle et le déploiement des applications.


