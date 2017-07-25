---
title: "Déployer des ressources Azure sur plusieurs groupes de ressources | Microsoft Docs"
description: "Montre comment cibler plusieurs groupes de ressources Azure pendant le déploiement."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: d8b041213b269775175a810e585103d3c538557f
ms.contentlocale: fr-fr
ms.lasthandoff: 06/17/2017


---

# <a name="deploy-azure-resources-to-more-than-one-resource-group"></a>Déployer des ressources Azure sur plusieurs groupes de ressources

En général, vous déployez toutes les ressources dans votre modèle sur un seul groupe de ressources. Toutefois, il existe des scénarios dans lesquels vous souhaitez déployer simultanément un ensemble de ressources à placer dans différents groupes de ressources. Par exemple, vous voudrez peut-être déployer la machine virtuelle de sauvegarde destinée à Azure Site Recovery sur un groupe de ressources et un emplacement distincts. Resource Manager vous permet d’utiliser des modèles imbriqués pour cibler des groupes de ressources différents de celui utilisé pour le modèle parent.

Le groupe de ressources est le conteneur de cycle de vie de l’application et sa collection de ressources. Vous créez le groupe de ressources en dehors du modèle et spécifiez le groupe de ressources à cibler lors du déploiement. Pour voir une présentation des groupes de ressources, consultez la page [Présentation d’Azure Resource Manager](resource-group-overview.md).

## <a name="example-template"></a>Exemple de modèle

Pour cibler une autre ressource, vous devez utiliser un modèle imbriqué ou lié au cours du déploiement. Le type de ressource `Microsoft.Resources/deployments` fournit un paramètre `resourceGroup` qui vous permet de spécifier un autre groupe de ressources pour le déploiement imbriqué. Tous les groupes de ressources doivent exister avant l’exécution du déploiement. L’exemple suivant déploie deux comptes de stockage, un dans le groupe de ressources spécifié pendant le déploiement et l’autre dans un groupe de ressources nommé `crossResourceGroupDeployment` :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "StorageAccountName1": {
            "type": "string"
        },
        "StorageAccountName2": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "crossResourceGroupDeployment",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[parameters('StorageAccountName2')]",
                            "apiVersion": "2015-06-15",
                            "location": "West US",
                            "properties": {
                                "accountType": "Standard_LRS"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('StorageAccountName1')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
                "accountType": "Standard_LRS"
            }
        }
    ]
}
```

Si vous définissez `resourceGroup`sur le nom d’un groupe de ressources qui n’existe pas, le déploiement échoue. Si vous n’indiquez pas de valeur pour `resourceGroup`, Resource Manager utilise le groupe de ressources parent.  

## <a name="deploy-the-template"></a>Déployer le modèle

Pour déployer l’exemple de modèle, vous pouvez utiliser le portail, Azure PowerShell ou Azure CLI. Pour Azure PowerShell ou d’Azure CLI, vous devez utiliser une version postérieure au mois d’avril 2017. Les exemples supposent que vous avez enregistré le modèle localement dans un fichier nommé **crossrgdeployment.json**.

Pour PowerShell :

```powershell
Login-AzureRmAccount

New-AzureRmResourceGroup -Name mainResourceGroup -Location "South Central US"
New-AzureRmResourceGroup -Name crossResourceGroupDeployment -Location "Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName mainResourceGroup `
  -TemplateFile c:\MyTemplates\crossrgdeployment.json
```

Pour Azure CLI :

```azurecli
az login

az group create --name mainResourceGroup --location "South Central US"
az group create --name crossResourceGroupDeployment --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group mainResourceGroup \
    --template-file crossrgdeployment.json
```

Une fois le déploiement terminé, deux groupes de ressources s’affichent. Chaque groupe de ressources contient un compte de stockage.

## <a name="use-resourcegroup-function"></a>Utiliser la fonction resourceGroup()

Pour des déploiements entre groupes de ressources, la [fonction resouceGroup()](resource-group-template-functions-resource.md#resourcegroup) produit un résultat différent selon la façon dont vous spécifiez le modèle imbriqué. 

Si vous incorporez un modèle dans un autre, la résolution de resouceGroup() dans le modèle imbriqué est le groupe de ressources parent. Un modèle incorporé utilise le format suivant :

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

Si vous liez à un modèle séparé, la résolution de resouceGroup() dans le modèle lié est le groupe de ressources imbriqué. Un modèle lié utilise le format suivant :

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour comprendre comment définir des paramètres dans votre modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déploiement d’un modèle privé avec un jeton SAP](resource-manager-powershell-sas-token.md).

