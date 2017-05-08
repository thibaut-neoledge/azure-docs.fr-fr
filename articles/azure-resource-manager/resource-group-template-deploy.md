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
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: f5119fef1fb0a316b4109ccbc4433f8c9a18d128
ms.lasthandoff: 04/20/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell

Cette rubrique explique comment utiliser Azure PowerShell avec les modèles Resource Manager pour déployer vos ressources dans Azure. Votre modèle peut être un fichier local ou un fichier externe disponible par le biais d’un URI.

Vous pouvez obtenir le modèle (storage.json) utilisé dans ces exemples dans l’article [Créer votre premier modèle Azure Resource Manager](resource-manager-create-first-template.md#final-template). Pour utiliser le modèle avec ces exemples, créez un fichier JSON et ajoutez le contenu copié.

## <a name="deploy-local-template"></a>Déployer un modèle local
Afin de prendre rapidement en main le déploiement, utilisez les applets de commande suivantes pour déployer un modèle local avec des paramètres inline. 

```powershell
Login-AzureRmAccount
 
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

Le déploiement peut prendre plusieurs minutes. Au terme, vous voyez un message qui inclut le résultat :

```powershell
ProvisioningState       : Succeeded
```

Dans l’exemple précédent, un groupe de ressources a été créé dans votre abonnement par défaut. Pour utiliser un autre abonnement, ajoutez l’applet de commande [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext) une fois connecté.

## <a name="deploy-external-template"></a>Déployer un modèle externe

Pour déployer un modèle externe, utilisez le paramètre **TemplateUri**. Le modèle peut se trouver sur n’importe quel URI accessible publiquement (par exemple, un fichier de compte de stockage).

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -storageNamePrefix contoso -storageSKU Standard_GRS
```

Vous pouvez protéger votre modèle en demandant un accès à l’aide d’un jeton de signature d’accès partagé (SAP). Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déploiement d’un modèle privé avec un jeton SAP](resource-manager-powershell-sas-token.md).

## <a name="parameter-files"></a>Fichiers de paramètres

Les exemples précédents vous ont montré comment transmettre des paramètres en tant que valeurs inline. Vous pouvez spécifier des valeurs de paramètre dans un fichier et transmettre ce fichier au cours du déploiement. 

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

Pour transmettre un fichier de paramètres local, utilisez le paramètre **TemplateParameterFile** :

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Pour transmettre un fichier de paramètres externe, utilisez le paramètre **TemplateParameterUri** :

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.parameters.json
```

Vous pouvez utiliser des paramètres inline et un fichier de paramètres local pendant la même opération de déploiement. Par exemple, vous pouvez spécifier certaines valeurs dans le fichier de paramètres local et ajouter d’autres valeurs inline pendant le déploiement. Si vous fournissez des valeurs pour un paramètre à la fois dans le fichier de paramètres local et inline, la valeur inline est prioritaire.

Cependant, lorsque vous utilisez un fichier de paramètres externe, vous ne pouvez pas passer d’autres valeurs inline ou tirées d’un fichier local. Lorsque vous spécifiez un fichier de paramètres dans le paramètre **TemplateParameterUri**, tous les paramètres inline sont ignorés. Fournissez toutes les valeurs de paramètre dans le fichier externe. Si votre modèle inclut une valeur sensible que vous ne pouvez pas inclure dans le fichier de paramètres, ajoutez cette valeur dans un coffre de clés, ou fournissez de manière dynamique toutes des valeurs de paramètre inline.

Si votre modèle inclut un paramètre utilisant le même nom que l’un des paramètres dans la commande PowerShell, PowerShell présente le paramètre de votre modèle avec le suffixe **FromTemplate**. Par exemple, un paramètre nommé **ResourceGroupName** dans votre modèle est en conflit avec le paramètre **ResourceGroupName** dans l’applet de commande [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment). Vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**. En général, vous devez éviter cette confusion en ne nommant pas les paramètres avec un nom identique à celui des paramètres utilisés pour les opérations de déploiement.

## <a name="test-a-deployment"></a>Tester un déploiement

Pour tester votre modèle et vos valeur de paramètre sans réellement déployer toutes les ressources, utilisez [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). Les mêmes options sont utilisées pour les fichiers locaux ou distants.

```powershell
Test-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

## <a name="log-deployment-content-for-debugging"></a>Consigner le contenu du déploiement à des fins de débogage

Les informations relatives aux opérations de déploiement sont automatiquement enregistrées dans les journaux d’activité. Cependant, pour consigner des informations supplémentaires sur le déploiement qui peuvent vous aider à résoudre des erreurs de déploiement, utilisez le paramètre **DeploymentDebugLogLevel**. Vous pouvez demander à ce que le contenu de la demande et/ou de la réponse soit consigné avec l’opération de déploiement.
   
```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All `
  -ResourceGroupName ExampleGroup -TemplateFile storage.json
```

Pour plus d’informations sur l’affichage des journaux, consultez [Afficher les journaux d’activité pour auditer les actions sur les ressources](resource-group-audit.md).

## <a name="export-resource-manager-template"></a>Exporter un modèle Resource Manager
Pour un groupe de ressources existant (déployé via PowerShell ou l'une des autres méthodes telles que le portail), vous pouvez afficher le modèle Resource Manager pour le groupe de ressources. L’exportation du modèle offre deux avantages :

1. Vous pouvez facilement automatiser les prochains déploiements de la solution, car l’ensemble de l’infrastructure est défini dans le modèle.
2. Vous pouvez vous familiariser avec la syntaxe de modèle en regardant dans la JSON (JavaScript Object Notation) qui représente votre solution.

Pour afficher le modèle d’un groupe de ressources, exécutez l’applet de commande [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup) .

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleResourceGroup
```

Pour plus d’informations, voir [Exporter un modèle Azure Resource Manager à partir de ressources existantes](resource-manager-export-template.md).


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Pour utiliser le mode complet, utilisez le paramètre `Mode` :

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```


## <a name="next-steps"></a>Étapes suivantes
* Pour accéder à un exemple de script complet qui déploie un modèle, consultez la page [Déploiement d’un modèle Azure Resource Manager](resource-manager-samples-powershell-deploy.md).
* Pour définir des paramètres dans le modèle, consultez [Création de modèles](resource-group-authoring-templates.md#parameters).
* Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déploiement d’un modèle privé avec un jeton SAP](resource-manager-powershell-sas-token.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).


