<properties
    pageTitle="Création de ressources Service Bus à l’aide de modèles Azure Resource Manager | Microsoft Azure"
    description="Utilisez les modèles Azure Resource Manager pour automatiser la création de ressources Service Bus"
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
    ms.date="07/11/2016"
    ms.author="sethm"/>

# Création de ressources Service Bus à l’aide de modèles Azure Resource Manager

Cet article montre comment créer et déployer des ressources Service Bus et Event Hubs à l'aide de modèles Azure Resource Manager, de PowerShell et du fournisseur de ressources Service Bus.

Les modèles Azure Resource Manager vous permettent de définir les ressources à déployer pour une solution et de spécifier les paramètres et variables qui permettent d'entrer des valeurs pour les différents environnements. Le modèle se compose de JSON et d'expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement. Pour plus d'informations sur l'écriture de modèles Azure Resource Manager et sur le format du modèle, consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

>[AZURE.NOTE] Les exemples de cet article montrent comment utiliser Azure Resource Manager pour créer un espace de noms et une entité de messagerie (file d'attente) Service Bus. Pour accéder à d’autres exemples de modèles, recherchez « Service Bus » dans la [Galerie de modèles de démarrage rapide Azure][].

## Modèles Resource Manager Service Bus et Event Hubs

Ces modèles Azure Resource Manager Service Bus et Event Hubs sont disponibles au téléchargement et au déploiement. Cliquez sur les liens suivants pour plus d'informations sur chacun d’eux, ainsi que des liens vers les modèles sur GitHub :

- [Création d'un espace de noms Service Bus](service-bus-resource-manager-namespace.md)
- [Créer un espace de noms Service Bus avec file d’attente](service-bus-resource-manager-namespace-queue.md)
- [Créer un espace de noms Service Bus par rubrique et abonnement](service-bus-resource-manager-namespace-topic.md)
- [Créer un espace de noms Service Bus avec file d'attente et règle d’autorisation](service-bus-resource-manager-namespace-auth-rule.md)
- [Créer un espace de noms Event Hubs avec un Event Hub et un groupe de consommateurs](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)

## Déployer avec PowerShell

La procédure suivante décrit comment utiliser PowerShell pour déployer un modèle Azure Resource Manager qui crée un espace de noms Service Bus de niveau **Standard** et une file d'attente au sein de cet espace de noms. Cet exemple est basé sur le modèle [Créer un espace de noms Service Bus avec file d'attente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue). Le flux de travail est approximativement le suivant :

1. Installez PowerShell.
2. Créez le modèle et (éventuellement) un fichier de paramètres.
2. Dans PowerShell, connectez-vous à votre compte Azure.
3. Créez un groupe de ressources s'il n'en existe pas.
4. Testez le déploiement.
5. Si vous le souhaitez, définissez le mode de déploiement.
6. Déployez le modèle.

Pour des informations complètes sur le déploiement de modèles Azure Resource Manager, consultez [Déploiement de ressources avec des modèles Azure Resource Manager][].

### Installer PowerShell

Installez PowerShell en suivant les instructions de la page [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md).

### Créer un modèle

Clonez ou copiez le modèle [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) à partir de GitHub :

```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
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
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### Créer un fichier de paramètres (facultatif)

Pour utiliser un fichier de paramètres facultatif, copiez le fichier [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json). Remplacez la valeur de `serviceBusNamespaceName` par le nom de l'espace de noms Service Bus que vous souhaitez créer dans ce déploiement, puis remplacez la valeur de `serviceBusQueueName` par le nom de la file d'attente que vous souhaitez créer.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

Pour plus d’informations, consultez la rubrique [Fichier de paramètres](../resource-group-template-deploy.md#parameter-file).

### Se connecter à Azure et définir l’abonnement Azure

À partir d’une invite de commandes PowerShell, exécutez la commande suivante :

```
Login-AzureRmAccount
```

Vous êtes invité à ouvrir une session sur votre compte Azure. Une fois connecté, exécutez la commande suivante pour afficher les abonnements disponibles.

```
Get-AzureRMSubscription
```

Cette commande renvoie la liste des abonnements Azure disponibles. Choisissez un abonnement pour la session en cours en exécutant la commande suivante. Remplacez `<YourSubscriptionId>` par le GUID de l’abonnement Azure que vous souhaitez utiliser.

```
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### Définir le groupe de ressources

Si vous n’avez pas de groupe de ressources, créez-en un avec la commande **New-AzureRmResourceGroup**. Indiquez le nom du groupe de ressources et l'emplacement que vous souhaitez utiliser. Par exemple :

```
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

En cas de réussite, un résumé du nouveau groupe de ressources s’affiche.

```
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### test du déploiement

Validez votre déploiement en exécutant l’applet de commande `Test-AzureRmResourceGroupDeployment`. Lorsque vous testez le déploiement, indiquez les paramètres exactement comme vous le feriez lors de l'exécution du déploiement.

```
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### Créer le déploiement

Pour créer le déploiement, exécutez la commande `New-AzureRmResourceGroupDeployment` et indiquez les paramètres nécessaires quand vous y êtes invité. Les paramètres incluent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL du fichier de modèle. Si le paramètre **Mode** n’est pas spécifié, la valeur par défaut **Incremental** est utilisée. Pour plus d’informations, consultez [Déploiements incrémentiels et complets](../resource-group-template-deploy.md#incremental-and-complete-deployments).

La commande suivante vous invite à entrer les trois paramètres dans la fenêtre PowerShell :

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Pour spécifier un fichier de paramètres à la place, utilisez la commande suivante.

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Vous pouvez également utiliser des paramètres inclus lorsque vous exécutez l'applet de commande de déploiement. La commande est la suivante :

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Pour exécuter un déploiement [complet](../resource-group-template-deploy.md#incremental-and-complete-deployments), affectez la valeur **Complet** au paramètre **Mode** :

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

### Vérifier le déploiement

Si les ressources sont déployées avec succès, un résumé du déploiement s’affiche dans la fenêtre PowerShell :

```
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## Étapes suivantes

Vous avez maintenant vu le flux de travail et les commandes de base pour le déploiement d'un modèle Azure Resource Manager. Pour plus d'informations, consultez les liens suivants :

- [Présentation d’Azure Resource Manager][]
- [Déployer des ressources à l’aide de modèles Azure Resource Manager][]
- [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md)


[Présentation d’Azure Resource Manager]: ../resource-group-overview.md
[Déploiement de ressources avec des modèles Azure Resource Manager]: ../resource-group-template-deploy.md
[Déployer des ressources à l’aide de modèles Azure Resource Manager]: ../resource-group-template-deploy.md
[Galerie de modèles de démarrage rapide Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus

<!---HONumber=AcomDC_0831_2016-->