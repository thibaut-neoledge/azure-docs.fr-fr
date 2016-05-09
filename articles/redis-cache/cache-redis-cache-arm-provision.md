<properties 
	pageTitle="Approvisionner un cache Redis | Microsoft Azure" 
	description="Utilisez un modèle Azure Resource Manager pour déployer un cache Redis Azure." 
	services="app-service" 
	documentationCenter="" 
	authors="steved0x" 
	manager="Erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2016" 
	ms.author="sdanie"/>

# Créer un cache Redis à l’aide d’un modèle

Dans cette rubrique, vous allez apprendre à créer un modèle Azure Resource Manager qui déploie un cache Redis Azure. Le cache peut être utilisé avec un compte de stockage existant pour conserver les données de diagnostic. Vous allez apprendre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins.

Actuellement, les paramètres de diagnostic sont partagés entre tous les caches de la même région d’un abonnement. La mise à jour d’un cache dans la région affecte tous les autres caches dans la région.

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

Pour le modèle complet, consultez le [modèle de cache Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

>[AZURE.NOTE] Des modèles ARM pour le nouveau [niveau Premium](cache-premium-tier-intro.md) sont disponibles.
>
>-    [Créer un cache Redis Premium avec le clustering](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
>-    [Créer un cache Redis Premium avec la persistance des données](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
>-    [Créer un cache Redis Premium avec un réseau virtuel et le clustering facultatif](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
>
>Pour connaître les derniers modèles, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/) et recherchez `Redis Cache`.

## Ce que vous allez déployer

Dans ce modèle, vous allez déployer un cache Redis Azure qui utilise un compte de stockage existant pour les données de diagnostic.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## Paramètres

Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée Paramètres, qui contient toutes les valeurs de paramètres. Vous devez définir un paramètre pour les valeurs qui varient selon le projet que vous déployez, ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent identiques. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Nous allons décrire chaque paramètre du modèle.

[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### redisCacheLocation

Emplacement du cache Redis. Pour de meilleures performances, utilisez le même emplacement que l’application à utiliser avec le cache.

    "redisCacheLocation": {
      "type": "string"
    }

### existingDiagnosticsStorageAccountName

Nom du compte de stockage existant à utiliser pour les diagnostics.

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### enableNonSslPort

Valeur booléenne qui indique s’il faut autoriser l’accès via des ports non-SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### diagnosticsStatus

Valeur qui indique si les diagnostics sont activés. Utilisez ON ou OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## Ressources à déployer

### Cache Redis

Crée le cache Redis Azure.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }


## Commandes pour l’exécution du déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### Interface de ligne de commande Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=AcomDC_0427_2016-->