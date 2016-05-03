<properties
    pageTitle="Créer un espace de noms Service Bus avec une file d’attente | Microsoft Azure"
    description="Créer un espace de noms Service Bus et d’une file d’attente à l’aide d’un modèle ARM"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="04/15/2016"
    ms.author="sethm;shvija"/>

# Créer un espace de noms Service Bus et d’une file d’attente à l’aide d’un modèle ARM

Cet article montre comment utiliser un modèle Azure Resource Manager (ARM) qui crée un espace de noms Service Bus et une file d'attente. Vous allez apprendre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins

Pour en savoir plus sur la création de modèles, consultez la rubrique [Création de modèles Azure Resource Manager][].

Pour le modèle complet, consultez le [modèle d'espace de noms et de file d’attente Service Bus][] sur GitHub.

>[AZURE.NOTE] Les modèles ARM suivants sont disponibles pour le téléchargement et le déploiement.
>
>-    [Créer un espace de noms Service Bus avec file d'attente et règle d’autorisation](service-bus-arm-namespace-auth-rule.md)
>-    [Créer un espace de noms Service Bus avec un concentrateur d'événements et un groupe de consommateurs](service-bus-arm-namespace-event-hub.md)
>-    [Créer un espace de noms Service Bus par rubrique et abonnement](service-bus-arm-namespace-topic.md)
>-    [Création d'un espace de noms Service Bus](service-bus-arm-namespace.md)
>
>Pour connaître les derniers modèles, recherchez Service Bus dans les [modèles de démarrage rapide Azure][].

## Qu'allez-vous déployer ?

Avec ce modèle, vous allez déployer un espace de noms Service Bus avec une file d’attente.

Les files d’attente permettent la remise de messages à un ou plusieurs destinataires concurrents sur le principe du premier entré, premier sorti (FIFO).

[En savoir plus sur les files d’attente Service Bus](service-bus-queues-topics-subscriptions.md).

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](./media/service-bus-arm-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## Paramètres

Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée `Parameters`, qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient selon le projet que vous déployez, ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent identiques. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Nous allons décrire chaque paramètre du modèle.

### serviceBusNamespaceName

Nom de l’espace de noms Service Bus à créer.

```
"serviceBusNamespaceName": {
"type": "string"
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

## Commandes pour exécuter le déploiement

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

Maintenant que vous avez créé et déployé des ressources à l'aide d’ARM, découvrez comment gérer ces ressources en consultant les articles suivants :

- [Gérer Azure Service Bus à l'aide d'Azure Automation](service-bus-automation-manage.md)
- [Gestion de Service Bus avec PowerShell](service-bus-powershell-how-to-provision.md)
- [Gérer les ressources Service Bus avec l'explorateur Service Bus](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Création de modèles Azure Resource Manager]: ../resource-group-authoring-templates.md
  [modèle d'espace de noms et de file d’attente Service Bus]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
  [modèles de démarrage rapide Azure]: https://azure.microsoft.com/documentation/templates/
  [Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0420_2016-->