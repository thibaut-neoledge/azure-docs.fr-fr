---
title: "Déployer des ressources sur Azure | Microsoft Docs"
description: "Utilisez Azure PowerShell ou l’interface de ligne de commande Azure pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 41d9476d4ce6b7afc2ba9c757e96db7e8e72d5ae
ms.openlocfilehash: a77354d0719240e5916b5ec945dad75e534802d3
ms.lasthandoff: 02/21/2017


---
# <a name="deploy-resources-to-azure"></a>Déployer des ressources sur Azure

Cette rubrique explique comment déployer des ressources sur votre abonnement Azure. Vous pouvez utiliser Azure PowerShell ou l’interface de ligne de commande Azure pour déployer un modèle Resource Manager qui définit l’infrastructure de votre solution.

Pour une introduction aux concepts de Resource Manager, consultez [Vue d'ensemble d’Azure Resource Manager](resource-group-overview.md).

## <a name="steps-for-deployment"></a>Étapes de déploiement

Cette rubrique suppose que vous déployez l[’exemple de modèle de stockage](#example-storage-template) dans cette rubrique. Vous pouvez utiliser un modèle différent, mais les paramètres que vous transmettez sont différents de ceux indiqués dans cette rubrique.

Après avoir créé un modèle, les étapes générales pour le déploiement de votre modèle sont les suivantes :

1. Se connecter à son compte
2. Sélectionnez l’abonnement à utiliser (uniquement si vous en avez plusieurs et souhaitez utiliser un abonnement qui n’est pas celui défini par défaut)
3. Créer un groupe de ressources
4. Déployer le modèle
5. Vérifier l’état de votre déploiement

Les sections suivantes vous expliquent comment effectuer ces étapes à l’aide de [PowerShell](#powershell) ou de [l’interface de ligne de commande Azure](#azure-cli).

## <a name="powershell"></a>PowerShell

1. Pour Installer Azure PowerShell, consultez [Prise en main des applets de commande Azure PowerShell](/powershell/azureps-cmdlets-docs).

2. Pour commencer le déploiement rapidement, utilisez les applets de commande suivantes :

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {subscription-id}

  New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json 
  ```

  L’applet de commande `Set-AzureRmContext` est nécessaire uniquement si vous souhaitez utiliser un abonnement autre que votre abonnement par défaut. Pour afficher tous vos abonnements et leurs numéros d’identification, utilisez :

  ```powershell
  Get-AzureRmSubscription
  ```

3. Le déploiement peut prendre plusieurs minutes. Une fois l’opération terminée, un message similaire à celui apparaît :

  ```powershell
  DeploymentName          : ExampleDeployment
  CorrelationId           : 07b3b233-8367-4a0b-b9bc-7aa189065665
  ResourceGroupName       : ExampleGroup
  ProvisioningState       : Succeeded
  ...
  ```

4. Pour vérifier que votre groupe de ressources et votre compte de stockage ont été déployés sur votre abonnement, utilisez :

  ```powershell
  Get-AzureRmResourceGroup -Name ExampleGroup
  Find-AzureRmResource -ResourceGroupNameEquals ExampleGroup
  ```

5. Vous pouvez spécifier des modèles de paramètres comme paramètres PowerShell lors du déploiement d’un modèle. Comme l’exemple précédent ne comportait aucun modèle de paramètre, les valeurs par défaut du modèle ont été utilisées. Pour déployer un autre compte de stockage et fournir des valeurs de paramètres pour le préfixe du nom de stockage et la référence du compte de stockage, utilisez :

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment2 -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json -storageNamePrefix "contoso" -storageSKU "Standard_GRS"
  ```

  Vous disposez maintenant de deux comptes de stockage dans votre groupe de ressources. 

## <a name="azure-cli"></a>Interface de ligne de commande Azure

1. Pour installer l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure 2.0](/cli/azure/install-az-cli2).

2. Pour commencer le déploiement rapidement, utilisez les commandes suivantes :

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create --name ExampleDeployment --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json
  ```

  La commande `az account set` est uniquement nécessaire si vous souhaitez utiliser un abonnement autre que votre abonnement par défaut. Pour afficher tous vos abonnements et leurs numéros d’identification, utilisez :

  ```azurecli
  az account list
  ```

3. Le déploiement peut prendre plusieurs minutes. Une fois l’opération terminée, un message similaire à celui apparaît :

  ```azurecli
  "provisioningState": "Succeeded",
  ```

4. Pour vérifier que votre groupe de ressources et votre compte de stockage ont été déployés sur votre abonnement, utilisez :

  ```azurecli
  az group show --name ExampleGroup
  az resource list --resource-group ExampleGroup
  ```

5. Vous pouvez spécifier des modèles de paramètres comme paramètres PowerShell lors du déploiement d’un modèle. Comme l’exemple précédent ne comportait aucun modèle de paramètre, les valeurs par défaut du modèle ont été utilisées. Pour déployer un autre compte de stockage et fournir des valeurs de paramètres pour le préfixe du nom de stockage et la référence du compte de stockage, utilisez :

  ```azurecli
  az group deployment create --name ExampleDeployment2 --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  Vous disposez maintenant de deux comptes de stockage dans votre groupe de ressources. 

## <a name="example-storage-template"></a>Exemple de modèle de stockage

Utilisez l’exemple de modèle suivant pour déployer un compte de stockage sur votre abonnement :

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’utilisation de PowerShell afin de déployer des modèles, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](/azure/azure-resource-manager/resource-group-template-deploy).
* Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Azure afin de déployer des modèles, consultez [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](/azure/azure-resource-manager/resource-group-template-deploy-cli).




