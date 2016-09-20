<properties
    pageTitle="Créer un espace de noms Event Hubs avec Event Hub et un groupe de consommateurs à l’aide d’un modèle Azure Resource Manager | Microsoft Azure"
    description="Créer un espace de noms Event Hubs avec Event Hub et un groupe de consommateurs à l’aide d’un modèle Azure Resource Manager"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>

# Créer un espace de noms Event Hubs avec Event Hub et un groupe de consommateurs à l’aide d’un modèle Azure Resource Manager

Cet article montre comment utiliser un modèle Azure Resource Manager qui crée un espace de noms Event Hubs avec un Event Hub et un groupe de consommateurs. Vous allez apprendre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins

Pour en savoir plus sur la création de modèles, consultez la rubrique [Création de modèles Azure Resource Manager][].

Pour le modèle complet, consultez le [modèle d’Event Hub et de groupe de consommateurs][] sur GitHub.

>[AZURE.NOTE]
Pour connaître les derniers modèles, recherchez Event Hubs dans la galerie de [modèles de démarrage rapide Azure][].

## Qu'allez-vous déployer ?

Avec ce modèle, vous allez déployer un espace de noms Event Hubs avec un Event Hub et un groupe de consommateurs.

[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) est un service de traitement des événements utilisé pour fournir des entrées d’événements et de télémétrie dans Azure à grande échelle, avec faible latence et fiabilité élevée.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## Paramètres

Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée `Parameters`, qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient selon le projet que vous déployez, ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent identiques. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Le modèle définit les paramètres suivants.

### eventHubNamespaceName

Nom de l’espace de noms Event Hubs à créer.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### eventHubName

Nom du Event Hub créé dans l’espace de noms Event Hubs.

```
"eventHubName": {
"type": "string"
}
```

### eventHubConsumerGroupName

Nom du groupe de consommateurs créé pour le Event Hub.

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### apiVersion

Version d’API du modèle.

```
"apiVersion": {
"type": "string"
}
```

## Ressources à déployer

Crée un espace de noms de type **Event Hubs**, avec un Event Hub et un groupe de consommateurs.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
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

## Commandes pour exécuter le déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## Interface de ligne de commande Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

## Étapes suivantes

Maintenant que vous avez créé et déployé des ressources à l’aide d’Azure Resource Manager, découvrez comment gérer ces ressources en consultant les articles suivants :

- [Gérer les ressources de hubs d'événements avec l'explorateur Service Bus](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Création de modèles Azure Resource Manager]: ../resource-group-authoring-templates.md
  [modèles de démarrage rapide Azure]: https://azure.microsoft.com/documentation/templates/?term=event+hubs
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [modèle d’Event Hub et de groupe de consommateurs]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/

<!---HONumber=AcomDC_0907_2016-->