---
title: "Déploiement des ressources avec le modèle et l’interface de ligne de commande Azure | Microsoft Docs"
description: "Utilisez Azure Resource Manager et l’interface de ligne de commande Azure pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: c889a609b8d49474216fe1dcfba69a881edb4133
ms.lasthandoff: 04/20/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure

Cette rubrique explique comment utiliser [Azure CLI 2.0](/cli/azure/install-az-cli2) avec les modèles Resource Manager pour déployer vos ressources dans Azure.  Votre modèle peut être un fichier local ou un fichier externe disponible par le biais d’un URI.

Vous pouvez obtenir le modèle (storage.json) utilisé dans ces exemples dans l’article [Créer votre premier modèle Azure Resource Manager](resource-manager-create-first-template.md#final-template). Pour utiliser le modèle avec ces exemples, créez un fichier JSON et ajoutez le contenu copié.

## <a name="deploy-local-template"></a>Déployer un modèle local

Afin de prendre rapidement en main le déploiement, utilisez les commandes suivantes pour déployer un modèle local avec des paramètres inline :

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

Le déploiement peut prendre plusieurs minutes. Au terme, vous voyez un message qui inclut le résultat :

```azurecli
"provisioningState": "Succeeded",
```

Dans l’exemple précédent, un groupe de ressources a été créé dans votre abonnement par défaut. Pour utiliser un autre abonnement, ajoutez l’applet de commande [Set-AzureRmContext](/cli/azure/account#set) une fois connecté.

## <a name="deploy-external-template"></a>Déployer un modèle externe

Pour déployer un modèle externe, utilisez le paramètre **template-uri**. Le modèle peut se trouver sur n’importe quel URI accessible publiquement (par exemple, un fichier de compte de stockage).
   
```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json" \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

Vous pouvez protéger votre modèle en demandant un accès à l’aide d’un jeton de signature d’accès partagé (SAP). Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déploiement d’un modèle privé avec un jeton SAP](resource-manager-cli-sas-token.md).

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

Pour transmettre un fichier de paramètres locaux, utilisez :

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-deployment"></a>Tester un déploiement

Pour tester votre modèle et vos valeur de paramètre sans réellement déployer toutes les ressources, utilisez [az group deployment validate](/cli/azure/group/deployment#validate). Les mêmes options sont utilisées pour les fichiers locaux ou distants.

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="debug"></a>Déboguer

Pour afficher des informations sur les opérations d’un déploiement non réussi, utilisez :
   
```azurecli
az group deployment operation list --resource-group ExampleGroup --name vmlinux --query "[*].[properties.statusMessage]"
```

Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).


## <a name="export-resource-manager-template"></a>Exporter un modèle Resource Manager
Pour un groupe de ressources existant (déployé via Azure CLI ou l'une des autres méthodes telles que le portail), vous pouvez afficher le modèle Resource Manager pour le groupe de ressources. L’exportation du modèle offre deux avantages :

1. Vous pouvez facilement automatiser les prochains déploiements de la solution, car l’ensemble de l’infrastructure est défini dans le modèle.
2. Vous pouvez vous familiariser avec la syntaxe de modèle en regardant dans la JSON (JavaScript Object Notation) qui représente votre solution.

Pour afficher le modèle d’un groupe de ressources, exécutez la commande [az group export](/cli/azure/group#export).

```azurecli
az group export --name ExampleGroup
```

Pour plus d’informations, voir [Exporter un modèle Azure Resource Manager à partir de ressources existantes](resource-manager-export-template.md).


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Pour utiliser le mode complet, utilisez le paramètre `mode` :

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```


## <a name="next-steps"></a>Étapes suivantes
* Pour accéder à un exemple de script complet qui déploie un modèle, consultez la page [Déploiement d’un modèle Azure Resource Manager](resource-manager-samples-cli-deploy.md).
* Pour définir des paramètres dans le modèle, consultez [Création de modèles](resource-group-authoring-templates.md#parameters).
* Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déploiement d’un modèle privé avec un jeton SAP](resource-manager-cli-sas-token.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).

