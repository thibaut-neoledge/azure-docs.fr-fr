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
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# Opérations de modèle avancées

Cette rubrique décrit l’opération de copie et les modèles imbriqués permettant d’effectuer des tâches plus avancées lors du déploiement de ressources sur Azure.

## copy

Permet de parcourir un tableau et d’utiliser chaque élément lors du déploiement d’une ressource.
   
L’exemple suivant déploie trois sites Web nommés examplecopy-Contoso, examplecopy-Fabrikam et examplecopy-Coho.

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

## Modèle imbriqué

Vous devrez parfois fusionner deux modèles ou lancer un modèle enfant à partir d’un parent. Pour ce faire, vous pouvez utiliser une ressource de déploiement dans le modèle principal pour déployer un modèle enfant. Indiquez l’URI du modèle imbriqué, comme illustré ci-dessous.

    "variables": {"templatelink":"https://www.contoso.com/ArmTemplates/newStorageAccount.json"}, 
    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {"uri":"[variables('templatelink')]","contentVersion":"1.0.0.0"}, 
           "parameters": { 
              "StorageAccountName":{"value":"[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

## Étapes suivantes
- [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md)
- [Fonctions des modèles Azure Resource Manager](./resource-group-template-functions.md)
- [Déploiement d’une application avec un modèle Azure Resource Manager](./resouce-group-template-deploy.md)
- [Présentation d’Azure Resource Manager](./resource-group-overview.md)

<!--HONumber=52-->
