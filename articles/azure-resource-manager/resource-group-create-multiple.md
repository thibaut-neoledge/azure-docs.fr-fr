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
ms.date: 11/02/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: b3972f3d407b3ba9529b36005c0856796c272095


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>Déployer plusieurs instances de ressources dans des modèles Azure Resource Manager
Cette rubrique explique comment procéder à une itération dans votre modèle Azure Resource Manager pour créer plusieurs instances d’une ressource.

## <a name="copy-copyindex-and-length"></a>copy, copyIndex et length
Dans la ressource que vous souhaitez créer à plusieurs reprises, vous pouvez définir un objet **copy** qui indique le nombre d’itérations à effectuer. La copie respecte le format suivant :

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Vous pouvez accéder à la valeur d’itération actuelle avec la fonction **copyIndex()**. L’exemple suivant utilise copyIndex avec la fonction concat pour construire un nom.

    [concat('examplecopy-', copyIndex())]

Lorsque vous créez plusieurs ressources à partir d'un tableau de valeurs, vous pouvez utiliser la fonction **length** pour spécifier le nombre. Vous passez le tableau en paramètre de la fonction length.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Vous pouvez appliquer l’objet de copie uniquement à une ressource de niveau supérieur. Vous ne pouvez pas l’appliquer à une propriété sur un type de ressource ou à une ressource enfant. Toutefois, cette rubrique montre comment spécifier plusieurs éléments pour une propriété et créer plusieurs instances d’une ressource enfant. L’exemple de pseudo-code suivant indique où la copie peut être appliquée :

    "resources": [
      {
        "type": "{provider-namespace-and-type}",
        "name": "parentResource",
        "copy": {  
          /* yes, copy can be applied here */
        },
        "properties": {
          "exampleProperty": {
            /* no, copy cannot be applied here */
          }
        },
        "resources": [
          {
            "type": "{provider-type}",
            "name": "childResource",
            /* copy can be applied if resource is promoted to top level */ 
          }
        ]
      }
    ] 

Même si vous ne pouvez pas appliquer la **copy** à une propriété, cette propriété fait toujours partie des itérations de la ressource qui contient la propriété. Par conséquent, vous pouvez utiliser **copyIndex()** au sein de la propriété pour spécifier des valeurs.

Il existe plusieurs scénarios dans lesquels vous pouvez souhaiter effectuer une itération sur une propriété dans une ressource. Par exemple, vous pouvez souhaiter spécifier plusieurs disques de données pour une machine virtuelle. Pour découvrir comment effectuer une itération sur une propriété, consultez la rubrique [Création de plusieurs instances lorsque l’opération copy ne fonctionne pas](#create-multiple-instances-when-copy-wont-work). 

Pour utiliser des ressources enfants, consultez [Créer plusieurs instances d’une ressource enfant](#create-multiple-instances-of-a-child-resource) (en anglais).

## <a name="use-index-value-in-name"></a>Utilisation de la valeur d’index dans le nom
Vous pouvez utiliser l’opération de copie pour créer plusieurs instances d’une ressource, nommées de manière unique en fonction de l’index d’incrémentation. Cela peut être utile si, par exemple, vous voulez ajouter un nombre unique à la fin de chaque nom de ressource déployée. Pour déployer trois sites web nommés :

* examplecopy-0
* examplecopy-1
* examplecopy-2

Utilisez le modèle suivant :

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## <a name="offset-index-value"></a>Valeur d’index de décalage
Dans l’exemple précédent, la valeur d’index s’étend de 0 à 2. Pour décaler la valeur d’index, vous pouvez transmettre une valeur dans la fonction **copyIndex()**, par exemple **copyIndex(1)**. Le nombre d’itérations à effectuer est toujours spécifié dans l’élément copy, mais la valeur de copyIndex est décalée en fonction de la valeur spécifiée. Ainsi, si nous utilisons le même modèle que dans l’exemple précédent, mais que nous spécifions **copyIndex(1)** , nous déploierons trois sites web nommés :

* examplecopy-1
* examplecopy-2
* examplecopy-3

## <a name="use-copy-with-array"></a>Utilisation de l’opération copy avec un tableau
L’opération copy se révèle utile lorsque vous travaillez avec des tableaux, car vous pouvez itérer sur chaque élément du tableau. Pour déployer trois sites web nommés :

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

Utilisez le modèle suivant :

    "parameters": { 
      "org": { 
         "type": "array", 
         "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

Bien sûr, vous pouvez définir le nombre de copies sur une valeur autre que la longueur du tableau. Par exemple, vous pouvez créer un tableau avec de nombreuses valeurs et ensuite passer une valeur de paramètre qui spécifie le nombre d'éléments du tableau à déployer. Dans ce cas, vous définissez le nombre de copies comme indiqué dans le premier exemple. 

## <a name="depend-on-resources-in-a-loop"></a>En fonction des ressources dans une boucle
Vous pouvez spécifier qu’une ressource est déployée après une autre ressource à l’aide de l’élément **dependsOn**. Pour déployer une ressource qui dépend de la collection de ressources dans une boucle, vous pouvez utiliser le nom de la boucle de copie dans l’élément **dependsOn**. L’exemple suivant montre comment déployer trois comptes de stockage avant de déployer la machine virtuelle. La définition complète de la machine virtuelle n’est pas affichée. Notez que le **nom** de l’élément de copie a la valeur **storagecopy** et que l’élément **dependsOn** pour la machine virtuelle est également défini sur **storagecopy**.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "resources": [
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "accountType": "Standard_LRS"
                 },
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

## <a name="create-multiple-instances-of-a-child-resource"></a>Création de plusieurs instances d’une ressource enfant
Vous ne pouvez pas utiliser une boucle de copie pour une ressource enfant. Pour créer plusieurs instances d’une ressource que vous définissez généralement comme imbriquée dans une autre ressource, vous devez plutôt créer cette ressource comme une ressource de niveau supérieur. Vous définissez la relation avec la ressource parente par le biais des propriétés **type** et **name**.

Par exemple, supposons que vous définissez généralement un jeu de données comme une ressource enfant dans une fabrique de données.

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

Pour créer plusieurs instances de jeux de données, vous devez le déplacer en dehors de la fabrique de données. Le jeu de données doit être au même niveau que la fabrique de données, mais il est toujours une ressource enfant de la fabrique de données. Vous conservez la relation entre le jeu de données et la fabrique de données par le biais des propriétés **type** et **name**. Étant donné que le type ne peut plus peut être déduit à partir de sa position dans le modèle, vous devez fournir le type qualifié complet au format suivant :

 **{espace de noms du fournisseur de ressource}/{type de ressource parente}/{type de ressource enfant}** 

Pour établir une relation parent/enfant avec une instance de la fabrique de données, fournissez un nom pour le jeu de données incluant le nom de la ressource parente. Utilisez le format de nom suivant :

**{nom de la ressource parente}/{nom de ressource enfant}**.  

L’exemple ci-après illustre l’implémentation :

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

## <a name="create-multiple-instances-when-copy-wont-work"></a>Création de plusieurs instances lorsque l’opération copy ne fonctionne pas
Vous pouvez uniquement utiliser l’opération **copy** sur les types de ressources, et non sur les propriétés d’un type de ressource. Cette exigence peut poser des problèmes lorsque vous souhaitez créer plusieurs instances d’un élément faisant partie d’une ressource. Il arrive souvent qu’un utilisateur souhaite créer plusieurs disques de données pour une machine virtuelle. Vous ne pouvez pas utiliser **copy** avec les disques de données, car **dataDisks** est une propriété sur la machine virtuelle et ne constitue pas son propre type de ressource. Au lieu de cela, vous créez un tableau avec autant de disques de données que nécessaire et vous transmettez le nombre réel de disques de données à créer. Dans la définition de la machine virtuelle, vous utilisez la fonction **take** pour obtenir uniquement le nombre d’éléments que vous souhaitez réellement obtenir à partir du tableau.

Un exemple complet de ce modèle est illustré dans le modèle de [création d’une machine virtuelle avec une sélection de disques de données dynamique](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

Les sections pertinentes du modèle de déploiement sont présentées dans l’exemple suivant. Une grande partie du modèle a été supprimée pour mettre l’accent sur les sections impliquées dans la création dynamique d’un nombre de disques de données. Notez le paramètre **numDataDisks** , qui vous permet de transmettre le nombre de disques à créer. 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

Vous pouvez utiliser la fonction **take** et l’élément **copy** ensemble lorsque vous avez besoin de créer plusieurs instances d’une ressource avec un nombre variable d’éléments pour une propriété. Par exemple, supposons que vous devez créer plusieurs machines virtuelles, mais que chaque machine virtuelle possède un nombre différent de disques de données. Pour donner à chaque disque de données un nom qui identifie la machine virtuelle associée, placez votre baie de disques de données dans un modèle séparé. Incluez les paramètres pour le nom de la machine virtuelle et le nombre de disques de données à retourner. Dans la section des sorties, retournez le nombre d’éléments spécifiés.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

Dans le modèle parent, vous incluez des paramètres pour le nombre de machines virtuelles et un tableau pour le nombre de disques de données pour chaque machine virtuelle.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

Dans la section de ressources, déployez plusieurs instances du modèle qui définit les disques de données. 

```
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

Dans la section de ressources, déployez plusieurs instances de la machine virtuelle. Pour les disques de données, référencez le déploiement imbriqué qui contient le nombre correct de disques de données et les noms corrects des disques de données.

```
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="return-values-from-a-loop"></a>Valeurs de retour depuis une boucle
Même si la création de plusieurs instances d’un type de ressource est pratique, il peut s’avérer difficile de retourner des valeurs à partir de cette boucle. Une manière de conserver et de retourner des valeurs consiste à utiliser l’élément **copy** (copie) avec un modèle imbriqué et à exécuter un aller-retour d’un tableau contenant toutes les valeurs à retourner. Par exemple, supposons que vous souhaitez créer plusieurs comptes de stockage et retourner le point de terminaison principal pour chacun d’eux. 

Tout d’abord, créez le modèle imbriqué qui crée le compte de stockage. Notez qu’il accepte un paramètre de tableau pour les URI d’objets blob. Vous utilisez ce paramètre pour un aller-retour de toutes les valeurs à partir des déploiements précédents. La sortie du modèle est un tableau qui concatène le nouvel URI d’objet blob selon l’URI précédent.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "indexValue": {
      "type":"int"
    },
    "blobURIs": {
        "type": "array",
      "defaultValue": []
    }
  },
    "variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id), parameters('indexValue'))]"
  },
    "resources": [
    {
        "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {  
      }
    }
    ],
    "outputs": {
      "result": {
        "type": "array",
      "value": "[concat(parameters('blobURIs'),split(reference(variables('storageName')).primaryEndpoints.blob, ','))]"
    }
  }
}
```

Maintenant, créez le modèle parent qui a une seule instance statique du modèle imbriqué, et exécute des boucles avec les autres instances du modèle imbriqué. Pour chaque instance du déploiement en boucle, transmettez un tableau qui contient la sortie du déploiement précédent.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberofStorage": { "type": "int", "minValue": 2 }
  },
  "resources": [
    {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate0",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": 0}
        }
      }
    },
    {
      "apiVersion": "2016-09-01",
      "name": "[concat('nestedTemplate', copyIndex(1))]",
      "type": "Microsoft.Resources/deployments",
      "copy": {
        "name": "storagecopy",
        "count": "[sub(parameters('numberofStorage'), 1)]"
      },
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": "[copyIndex(1)]"},
          "blobURIs": {"value": "[reference(concat('nestedTemplate', copyIndex())).outputs.result.value]"}
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "object",
      "value": "[reference(concat('nestedTemplate', sub(parameters('numberofStorage'), 1))).outputs.result]"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les sections d’un modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour obtenir la liste des fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour savoir comment déployer votre modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).




<!--HONumber=Jan17_HO4-->


