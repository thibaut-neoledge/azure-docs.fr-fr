<properties
    pageTitle="Créer un espace de noms Service Bus avec une file d’attente à l’aide d’un modèle Azure Resource Manager | Microsoft Azure"
    description="Créer un espace de noms Service Bus et une file d’attente à l’aide d’un modèle Azure Resource Manager"
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm;shvija"/>

# Créer un espace de noms Service Bus et une file d’attente à l’aide d’un modèle Azure Resource Manager

Cet article montre comment utiliser un modèle Azure Resource Manager qui crée un espace de noms Service Bus et une file d’attente. Vous allez apprendre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins.

Pour en savoir plus sur la création de modèles, consultez la rubrique [Création de modèles Azure Resource Manager][].

Pour le modèle complet, consultez le [modèle d'espace de noms et de file d’attente Service Bus][] sur GitHub.

>[AZURE.NOTE] Les modèles Azure Resource Manager suivants sont disponibles au téléchargement et au déploiement.
>
>-    [Créer un espace de noms Service Bus avec file d'attente et règle d’autorisation](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Créer un espace de noms Service Bus par rubrique et abonnement](service-bus-resource-manager-namespace-topic.md)
>-    [Création d'un espace de noms Service Bus](service-bus-resource-manager-namespace.md)
>-    [Créer un espace de noms Event Hubs avec un Event Hub et un groupe de consommateurs](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>
>Pour connaître les derniers modèles, recherchez Service Bus dans la galerie de [modèles de démarrage rapide Azure][].

## Qu'allez-vous déployer ?

Avec ce modèle, vous allez déployer un espace de noms Service Bus avec une file d’attente.

Les [files d’attente Service Bus](service-bus-queues-topics-subscriptions.md#queues) permettent de remettre des messages selon le principe du premier entré, premier sorti (FIFO) à un ou plusieurs destinataires concurrents.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## Paramètres

Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée `Parameters`, qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient selon le projet que vous déployez, ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent identiques. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Le modèle définit les paramètres suivants.

### serviceBusNamespaceName

Nom de l’espace de noms Service Bus à créer.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### serviceBusQueueName

Nom de la file d’attente créée dans l’espace de noms Service Bus.

```
"serviceBusQueueName": {
"type": "string"
}
```

### serviceBusApiVersion

La version de l’API Service Bus du modèle.

```
"serviceBusApiVersion": {
"type": "string"
}
```

## Ressources à déployer

Crée un espace de noms Service Bus standard de type **Messagerie**, avec une file d’attente.

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## Commandes pour l’exécution du déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## Interface de ligne de commande Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## Étapes suivantes

Maintenant que vous avez créé et déployé des ressources à l’aide d’Azure Resource Manager, découvrez comment gérer ces ressources en consultant les articles suivants :

- [Gérer Azure Service Bus à l'aide d'Azure Automation](../service-bus/service-bus-automation-manage.md)
- [Gestion de Service Bus avec PowerShell](../service-bus/service-bus-powershell-how-to-provision.md)
- [Gérer les ressources Service Bus avec l'explorateur Service Bus](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Création de modèles Azure Resource Manager]: ../resource-group-authoring-templates.md
  [modèle d'espace de noms et de file d’attente Service Bus]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
  [modèles de démarrage rapide Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0928_2016-->