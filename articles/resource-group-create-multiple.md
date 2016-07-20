<properties
   pageTitle="Déploiement de plusieurs instances de ressources | Microsoft Azure"
   description="Utilisez l’opération de copie et les tableaux dans un modèle Azure Resource Manager pour effectuer une itération à plusieurs reprises lors du déploiement de ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/30/2016"
   ms.author="tomfitz"/>

# Création de plusieurs instances de ressources dans Azure Resource Manager

Cette rubrique explique comment procéder à une itération dans votre modèle Azure Resource Manager pour créer plusieurs instances d’une ressource.

## copy, copyIndex et length

Dans la ressource que vous souhaitez créer à plusieurs reprises, vous pouvez définir un objet **copy** qui indique le nombre d’itérations à effectuer. La copie respecte le format suivant :

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Vous pouvez accéder à la valeur de l’itération avec la fonction **copyIndex()**, comme illustré ci-dessous dans la fonction concaténée.

    [concat('examplecopy-', copyIndex())]

Lorsque vous créez plusieurs ressources à partir d'un tableau de valeurs, vous pouvez utiliser la fonction **length** pour spécifier le nombre. Vous passez le tableau en paramètre de la fonction length.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## Utilisation de la valeur d’index dans le nom

Vous pouvez utiliser l’opération de copie pour créer plusieurs instances d’une ressource, nommées de manière unique en fonction de l’index d’incrémentation. Cela peut être utile si, par exemple, vous voulez ajouter un nombre unique à la fin de chaque nom de ressource déployée. Pour déployer trois sites web nommés :

- examplecopy-0
- examplecopy-1
- examplecopy-2

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

## Valeur d’index de décalage

Vous noterez que dans l’exemple précédent, la valeur d’index va de 0 à 2. Pour décaler la valeur d’index, vous pouvez transmettre une valeur dans la fonction **copyIndex()**, par exemple **copyIndex(1)**. Le nombre d’itérations à effectuer est toujours spécifié dans l’élément copy, mais la valeur de copyIndex est décalée en fonction de la valeur spécifiée. Ainsi, si nous utilisons le même modèle que dans l’exemple précédent, mais que nous spécifions **copyIndex(1)**, nous déploierons trois sites web nommés :

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Utilisation de l’opération copy avec un tableau
   
L’opération copy se révèle particulièrement utile lorsque vous travaillez avec des tableaux, car vous pouvez itérer sur chaque élément du tableau. Pour déployer trois sites web nommés :

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

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

Bien sûr, vous définissez le nombre de copies sur une valeur autre que la longueur du tableau. Par exemple, vous pouvez créer un tableau avec de nombreuses valeurs et ensuite passer une valeur de paramètre qui spécifie le nombre d'éléments du tableau à déployer. Dans ce cas, vous définissez le nombre de copies comme indiqué dans le premier exemple.

## Selon les ressources dans une boucle

Vous pouvez spécifier le déploiement d’une ressource après une autre ressource à l’aide de l’élément **dependsOn**. Quand vous devez déployer une ressource qui dépend de la collection de ressources dans une boucle, vous pouvez utiliser le nom de la boucle de copie dans l’élément **dependsOn**. L’exemple suivant montre comment déployer 3 comptes de stockage avant de déployer la machine virtuelle. La définition complète de la machine virtuelle n’est pas affichée. Notez que le **nom** de l’élément de copie a la valeur **storagecopy** et que l’élément **dependsOn** pour la machine virtuelle est également défini sur **storagecopy**.

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

## Itération sur une ressource imbriquée

Vous ne pouvez pas utiliser une boucle de copie pour une ressource imbriquée. Si vous voulez créer plusieurs instances d’une ressource que vous définissez généralement comme imbriquée dans une autre ressource, vous devez plutôt créer la ressource en tant que ressource de niveau supérieur et définir la relation avec la ressource parente au moyen des propriétés **type** et **name**.

Par exemple, supposons que vous définissiez généralement un jeu de données comme une ressource imbriquée dans une fabrique de données.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
Pour créer plusieurs instances de jeux de données, vous devez changer votre modèle comme indiqué ci-dessous. Notez que la propriété type indique un nom complet, et la propriété name le nom de la fabrique de données.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## Création de plusieurs instances lorsque l’opération copy ne fonctionne pas

Vous pouvez uniquement utiliser l’opération **copy** sur les types de ressources, et non sur les propriétés d’un type de ressource. Cela peut poser des problèmes lorsque vous souhaitez créer plusieurs instances d’un élément qui fait partie d’une seule ressource. Il arrive souvent qu’un utilisateur souhaite créer plusieurs disques de données pour une machine virtuelle. Vous ne pouvez pas utiliser **copy** avec les disques de données, car **dataDisks** est une propriété sur la machine virtuelle et ne constitue pas un type de ressource propre. Au lieu de cela, vous créez un tableau avec autant de disques de données que nécessaire et vous transmettez le nombre réel de disques de données à créer. Dans la définition de la machine virtuelle, vous utilisez la fonction **take** pour obtenir uniquement le nombre d’éléments que vous souhaitez réellement obtenir à partir du tableau.

Un exemple complet de ce modèle est illustré dans le modèle de [création d’une machine virtuelle avec une sélection de disques de données dynamique](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/).

Les sections pertinentes du modèle de déploiement sont présentées ci-dessous. Une grande partie du modèle a été supprimée pour mettre l’accent sur les sections impliquées dans la création dynamique d’un nombre de disques de données. Notez le paramètre **numDataDisks**, qui vous permet de transmettre le nombre de disques à créer.

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


## Étapes suivantes
- Pour en savoir plus sur les sections d’un modèle, consultez [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour obtenir la liste des fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](./resource-group-template-functions.md).
- Pour savoir comment déployer votre modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0706_2016-->