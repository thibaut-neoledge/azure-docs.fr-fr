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
   ms.date="06/13/2016"
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

## Utilisation avec un tableau
   
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

## Création de plusieurs disques de données pour une machine virtuelle

Il arrive souvent qu’un utilisateur souhaite créer plusieurs disques de données pour une machine virtuelle. Vous ne pouvez pas utiliser **copy** avec les disques de données, car **dataDisks** est une propriété sur la machine virtuelle et ne constitue pas un type de ressource propre. **copie** fonctionne uniquement avec des ressources. Au lieu de cela, vous créez un modèle lié qui définit le nombre de disques de données dont vous aurez besoin, puis génère un tableau avec le nombre de disques de données spécifié. À partir du modèle de déploiement, vous liez à ce modèle et transmettez le nombre de disques de données à retourner. Dans votre modèle de déploiement, vous définissez la propriété **dataDisks** sur la valeur de sortie à partir du modèle lié.

Un exemple complet de ce modèle est illustré dans le modèle de [création d’une machine virtuelle avec une sélection de disques de données dynamique](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/).

Les sections pertinentes du modèle de déploiement sont présentées ci-dessous. Une grande partie du modèle a été supprimée pour mettre l’accent sur les sections impliquées dans la création dynamique d’un nombre de disques de données. Notez le paramètre **numDataDisks**, qui vous permet de transmettre le nombre de disques à créer. Un modèle lié nommé **diskSelection** est spécifié dans la section de ressources. La sortie à partir de ce modèle lié est affectée à la propriété **dataDisks** sur la machine virtuelle.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        ...
        "numDataDisks": {
          "type": "string",
          "allowedValues": [
            "1",
            "2",
            "3",
            "4",
            "5",
            "6",
            "7",
            "8",
            "9",
            "10",
            "11",
            "12",
            "13",
            "14",
            "15",
            "16",
            "32"
          ],
          "metadata": {
            "description": "This parameter allows the user to select the number of disks they want"
          }
        }
      },
      "variables": {
        "artifactsLocation": "https://raw.githubusercontent.com/singhkay/azure-quickstart-templates/master/201-vm-dynamic-data-disks-selection/", 
        "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
        "sizeOfDataDisksInGB": 100,
        "diskCaching": "ReadWrite",
        ...
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "diskSelection",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "uri": "[concat(variables('artifactsLocation'), 'disksSelector', '.json')]",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "numDataDisks": {
                "value": "[parameters('numDataDisks')]"
              },
              "diskStorageAccountName": {
                "value": "[variables('storageAccountName')]"
              },
              "diskCaching": {
                "value": "[variables('diskCaching')]"
              },
              "diskSizeGB": {
                "value": "[variables('sizeOfDataDisksInGB')]"
              }
            }
          }
        },
        ...
        {
          "type": "Microsoft.Compute/virtualMachines",
          "properties": {
            "storageProfile": {
              ...
              "dataDisks": "[reference('diskSelection').outputs.dataDiskArray.value]"
            },
            ...
          }
        }
      ]
    }

Le modèle lié définit le tableau à renvoyer. Le modèle ci-dessous omet la répétition des définitions de disque de 3 à 32, mais, dans votre modèle réel, vous devez inclure toutes les définitions. Si vous avez besoin de plus de 32 disques de données, vous pouvez continuer le modèle. Notez qu’aucune ressource n’est déployée par le biais de ce modèle. Au lieu de cela, elle renvoie simplement un tableau avec le nombre d’objets nécessaire pour définir le disque de données.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numDataDisks": {
      "type": "string",
      "allowedValues": [
        "1",
        "2",
        "3",
        "4",
        "5",
        "6",
        "7",
        "8",
        "9",
        "10",
        "11",
        "12",
        "13",
        "14",
        "15",
        "16",
        "32"
      ],
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    },
    "diskStorageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the storage account where the data disks are stored"
      }
    },
    "diskCaching": {
      "type": "string",
      "allowedValues": [
        "None",
        "ReadOnly",
        "ReadWrite"
      ],
      "metadata": {
        "description": "Caching type for the data disks"
      }
    },
    "diskSizeGB": {
      "type": "int",
      "minValue": 1,
      "maxValue": 1023,
      "metadata": {
        "description": "Size of the data disks"
      }
    }
  },
  "variables": {
    "disksArray": {
      "1": "[variables('dataDisks')['1']]",
      "2": "[concat(variables('dataDisks')['1'], variables('dataDisks')['2'])]",
      "3": "[concat(variables('dataDisks')['1'], variables('dataDisks')['2'], variables('dataDisks')['3'])]",
      "4": "[variables('diskDeltas')['4delta']]",
      "5": "[concat(variables('diskDeltas')['4delta'], variables('dataDisks')['5'])]",
      "6": "[concat(variables('diskDeltas')['4delta'], variables('dataDisks')['5'], variables('dataDisks')['6'])]",
      "7": "[concat(variables('diskDeltas')['4delta'], variables('dataDisks')['5'], variables('dataDisks')['6'], variables('dataDisks')['7'])]",
      "8": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'])]",
      "9": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('dataDisks')['9'])]",
      "10": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('dataDisks')['9'], variables('dataDisks')['10'])]",
      "11": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('dataDisks')['9'], variables('dataDisks')['10'], variables('dataDisks')['11'])]",
      "12": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'])]",
      "13": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('dataDisks')['13'])]",
      "14": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('dataDisks')['13'], variables('dataDisks')['14'])]",
      "15": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('dataDisks')['13'], variables('dataDisks')['14'], variables('dataDisks')['15'])]",
      "16": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('diskDeltas')['16delta'])]",
      "32": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('diskDeltas')['16delta'], variables('diskDeltas')['32delta'])]"
    },
    "dataDisks": {
      "1": [
        {
          "name": "datadisk1",
          "lun": 0,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ],
      "2": [
        {
          "name": "datadisk2",
          "lun": 1,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ],
      "3": [
        {
          "name": "datadisk3",
          "lun": 2,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ],
      ...
      "32": [
        {
          "name": "datadisk32",
          "lun": 31,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk32.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ]
    },
    "_comment2": "The delta arrays below build the difference from 0 to 4, 4 to 8, 8 to 12 disks and so on",
    "diskDeltas": {
      "4delta": [
        "[variables('dataDisks')['1'][0]]",
        "[variables('dataDisks')['2'][0]]",
        "[variables('dataDisks')['3'][0]]",
        "[variables('dataDisks')['4'][0]]"
      ],
      "8delta": [
        "[variables('dataDisks')['5'][0]]",
        "[variables('dataDisks')['6'][0]]",
        "[variables('dataDisks')['7'][0]]",
        "[variables('dataDisks')['8'][0]]"
      ],
      "12delta": [
        "[variables('dataDisks')['9'][0]]",
        "[variables('dataDisks')['10'][0]]",
        "[variables('dataDisks')['11'][0]]",
        "[variables('dataDisks')['12'][0]]"
      ],
      "16delta": [
        "[variables('dataDisks')['13'][0]]",
        "[variables('dataDisks')['14'][0]]",
        "[variables('dataDisks')['15'][0]]",
        "[variables('dataDisks')['16'][0]]"
      ],
      "32delta": [
        "[variables('dataDisks')['17'][0]]",
        "[variables('dataDisks')['18'][0]]",
        "[variables('dataDisks')['19'][0]]",
        "[variables('dataDisks')['20'][0]]",
        "[variables('dataDisks')['21'][0]]",
        "[variables('dataDisks')['22'][0]]",
        "[variables('dataDisks')['23'][0]]",
        "[variables('dataDisks')['24'][0]]",
        "[variables('dataDisks')['25'][0]]",
        "[variables('dataDisks')['26'][0]]",
        "[variables('dataDisks')['27'][0]]",
        "[variables('dataDisks')['28'][0]]",
        "[variables('dataDisks')['29'][0]]",
        "[variables('dataDisks')['30'][0]]",
        "[variables('dataDisks')['31'][0]]",
        "[variables('dataDisks')['32'][0]]"
      ]
    }
  },
  "resources": [],
  "outputs": {
    "dataDiskArray": {
      "type": "array",
      "value": "[variables('disksArray')[parameters('numDataDisks')]]"
    }
  }
}

```

## Étapes suivantes
- Pour en savoir plus sur les sections d’un modèle, consultez [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour obtenir la liste des fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](./resource-group-template-functions.md).
- Pour savoir comment déployer votre modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0615_2016-->