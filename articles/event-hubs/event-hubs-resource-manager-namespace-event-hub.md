---
title: "Créer un espace de noms Event Hubs avec Event Hub et un groupe de consommateurs à l’aide d’un modèle Azure Resource Manager | Microsoft Docs"
description: "Créer un espace de noms Event Hubs avec Event Hub et un groupe de consommateurs à l’aide d’un modèle Azure Resource Manager"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/21/2016
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: a9e31954983fa673258917785a5bd828f1970fa1
ms.openlocfilehash: 92ec109c6cf9e3a2792ed68dfd96f86f5f9ae339


---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Créer un espace de noms Event Hubs avec Event Hub et un groupe de consommateurs à l’aide d’un modèle Azure Resource Manager
Cet article montre comment utiliser un modèle Azure Resource Manager qui crée un espace de noms Event Hubs avec un Event Hub et un groupe de consommateurs. Vous allez apprendre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager][Création de modèles Azure Resource Manager].

Pour le modèle complet, voir [modèle d’Event Hub et de groupe de consommateurs][modèle d’Event Hub et de groupe de consommateurs] sur GitHub.

> [!NOTE]
> Pour rechercher les derniers modèles, recherchez « Event Hubs » dans la galerie [Modèles de démarrage rapide Azure][Modèles de démarrage rapide Azure].
> 
> 

## <a name="what-will-you-deploy"></a>Qu'allez-vous déployer ?
Avec ce modèle, vous allez déployer un espace de noms Event Hubs avec un Event Hub et un groupe de consommateurs.

[Event Hubs](event-hubs-what-is-event-hubs.md) est un service de traitement des événements utilisé pour fournir des entrées d’événements et de télémétrie dans Azure à grande échelle, avec faible latence et fiabilité élevée.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres
Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée `Parameters` , qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient selon le projet que vous déployez, ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent identiques. Chaque valeur de paramètre dans le modèle définit les ressources déployées.

Le modèle définit les paramètres suivants.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName
Nom de l’espace de noms Event Hubs à créer.

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName
Nom du Event Hub créé dans l’espace de noms Event Hubs.

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName
Nom du groupe de consommateurs créé pour le Event Hub.

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion
Version d’API du modèle.

```json
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Ressources à déployer
Crée un espace de noms de type **Event Hubs**, avec un Event Hub et un groupe de consommateurs.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Interface de ligne de commande Azure
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[Création de modèles Azure Resource Manager]: ../resource-group-authoring-templates.md
[Modèles de démarrage rapide Azure]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Utilisation d’Azure PowerShell avec Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[modèle d’Event Hub et de groupe de consommateurs]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/



<!--HONumber=Nov16_HO5-->


