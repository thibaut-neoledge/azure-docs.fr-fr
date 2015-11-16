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
   ms.date="10/20/2015"
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

## Étapes suivantes
- Pour en savoir plus sur les sections d’un modèle, consultez [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour obtenir la liste des fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](./resource-group-template-functions.md).
- Pour savoir comment déployer votre modèle, consultez [Déploiement d'une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=Nov15_HO2-->