<properties
   pageTitle="Opérations de modèle avancées d’Azure Resource Manager"
   description="Décrit comment utiliser les modèles imbriqués et l’opération de copie dans un modèle Azure Resource Manager lors du déploiement d’applications dans Azure."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="06/29/2015"
   ms.author="tomfitz"/>

# Opérations de modèle avancées

Cette rubrique décrit l’opération de copie et les modèles imbriqués permettant d’effectuer des tâches plus avancées lors du déploiement de ressources sur Azure.

## copy

Vous permet d’itérer à plusieurs reprises lors du déploiement d’une ressource.
   
L’opération copy se révèle particulièrement utile lorsque vous travaillez avec des tableaux, car vous pouvez itérer sur chaque élément du tableau. La fonction **copyIndex()** renvoie la valeur actuelle de l’itération. Vous pouvez déployer trois sites web nommés :

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

avec le modèle suivant.

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      },
      "count": { 
         "type": "int", 
         "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

Vous pouvez également utiliser l’opération copy sans tableau. Par exemple, vous avez la possibilité d’ajouter un nombre incrémentiel à la fin du nom de chaque ressource déployée. Vous pouvez déployer trois sites web nommés :

- examplecopy-0
- examplecopy-1
- examplecopy-2

avec le modèle suivant.

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
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

Vous noterez que dans l’exemple précédent, la valeur d’index va de 0 à 2. Pour décaler la valeur d’index, vous pouvez transmettre une valeur dans la fonction **copyIndex()**, par exemple **copyIndex(1)**. Le nombre d’itérations à effectuer est toujours spécifié dans l’élément copy, mais la valeur de copyIndex est décalée en fonction de la valeur spécifiée. Ainsi, si nous utilisons le même modèle que dans l’exemple précédent, mais que nous spécifions **copyIndex(1)**, nous déploierons trois sites web nommés :

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Modèle imbriqué

Vous pouvez avoir besoin de fusionner deux modèles ou de lancer un modèle enfant à partir d’un parent. Pour ce faire, vous pouvez utiliser une ressource de déploiement dans le modèle principal qui pointe vers le modèle imbriqué. Vous définissez la propriété **templateLink** sur l’URI du modèle imbriqué. Vous pouvez fournir des valeurs de paramètre pour le modèle imbriqué en spécifiant les valeurs directement dans votre modèle ou en créant un lien vers un fichier de paramètres. Le premier exemple utilise la propriété **parameters** pour spécifier directement une valeur de paramètre.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

L’exemple suivant utilise la propriété **parametersLink** pour créer un lien vers un fichier de paramètres.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/ArmTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

## Étapes suivantes
- [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md)
- [Fonctions des modèles Azure Resource Manager](./resource-group-template-functions.md)
- [Déploiement d’une application avec un modèle Azure Resource Manager](azure-portal/resource-group-template-deploy.md)
- [Présentation du gestionnaire des ressources Azure](./resource-group-overview.md)

<!---HONumber=July15_HO3-->