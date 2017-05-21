---
title: "Déploiement de plusieurs instances de ressources Azure | Microsoft Docs"
description: "Utilisez l’opération de copie et les tableaux dans un modèle Azure Resource Manager pour effectuer une itération à plusieurs reprises lors du déploiement de ressources."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: e98fa067c0ed385fe20f66645311c9fd51cd6456
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Déployer plusieurs instances d’une ressource ou d’une propriété dans des modèles Azure Resource Manager
Cette rubrique explique comment procéder à une itération dans votre modèle Azure Resource Manager pour créer plusieurs instances d’une ressource.

## <a name="resource-iteration"></a>Itération de ressource
Pour créer plusieurs instances d’un type de ressource, ajoutez un élément `copy` au type de ressource. Dans l’élément copy, vous indiquez le nombre d’itérations et un nom pour cette boucle. La valeur de décompte doit être un entier positif et ne pas dépasser 800. Resource Manager crée les ressources en parallèle. Par conséquent, l’ordre de création n’est pas garanti. Pour créer des ressources itérées dans une séquence, consultez [Boucle séquentielle pour modèles Azure Resource Manager](resource-manager-sequential-loop.md). 

La ressource à créer plusieurs fois prend le format suivant :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Notez que le nom de chaque ressource inclut la fonction `copyIndex()`, qui renvoie l’itération actuelle de la boucle. `copyIndex()` est basé sur zéro. Si bien que l’exemple suivant :

```json
"name": "[concat('storage', copyIndex())]",
```

Crée les noms suivants :

* storage0
* storage1
* storage2.

Pour décaler la valeur d’index, vous pouvez transmettre une valeur dans la fonction copyIndex(). Le nombre d’itérations à effectuer est toujours spécifié dans l’élément copy, mais la valeur de copyIndex est décalée en fonction de la valeur spécifiée. Si bien que l’exemple suivant :

```json
"name": "[concat('storage', copyIndex(1))]",
```

Crée les noms suivants :

* storage1
* storage2
* storage3

L’opération copy se révèle utile lorsque vous travaillez avec des tableaux, car vous pouvez itérer sur chaque élément du tableau. Utilisez la fonction `length` sur le tableau pour spécifier le nombre d’itérations, et `copyIndex` pour récupérer l’index actuel dans le tableau. Si bien que l’exemple suivant :

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

Crée les noms suivants :

* storagecontoso
* storagefabrikam
* storagecoho

## <a name="depend-on-resources-in-a-loop"></a>En fonction des ressources dans une boucle
Vous spécifiez qu’une ressource est déployée après une autre ressource à l’aide de l’élément `dependsOn`. Pour déployer une ressource qui dépend de la collection de ressources dans une boucle, vous pouvez utiliser le nom de la boucle de copie dans l’élément dependsOn. L’exemple suivant montre comment déployer trois comptes de stockage avant de déployer la machine virtuelle. La définition complète de la machine virtuelle n’est pas affichée. Notez que le nom de l’élément de copie a la valeur `storagecopy` et que l’élément dependsOn pour la machine virtuelle est également défini sur `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## <a name="create-multiple-instances-of-a-child-resource"></a>Création de plusieurs instances d’une ressource enfant
Vous ne pouvez pas utiliser une boucle de copie pour une ressource enfant. Pour créer plusieurs instances d’une ressource que vous définissez généralement comme imbriquée dans une autre ressource, vous devez plutôt créer cette ressource comme une ressource de niveau supérieur. Vous définissez la relation avec la ressource parente par le biais des propriétés type et name.

Par exemple, supposons que vous définissez généralement un jeu de données comme une ressource enfant dans une fabrique de données.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

Pour créer plusieurs instances de jeux de données, vous devez le déplacer en dehors de la fabrique de données. Le jeu de données doit être au même niveau que la fabrique de données, mais il est toujours une ressource enfant de la fabrique de données. Vous conservez la relation entre le jeu de données et la fabrique de données par le biais des propriétés type et name. Étant donné que le type ne peut plus peut être déduit à partir de sa position dans le modèle, vous devez fournir le type qualifié complet au format : `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Pour établir une relation parent/enfant avec une instance de la fabrique de données, fournissez un nom pour le jeu de données incluant le nom de la ressource parente. Utilisez le format : `{parent-resource-name}/{child-resource-name}`.  

L’exemple ci-après illustre l’implémentation :

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les sections d’un modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour créer des ressources itérées dans une séquence, consultez [Boucle séquentielle pour modèles Azure Resource Manager](resource-manager-sequential-loop.md).
* Pour savoir comment déployer votre modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).


