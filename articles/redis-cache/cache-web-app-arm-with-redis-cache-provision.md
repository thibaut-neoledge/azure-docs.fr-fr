<properties 
	pageTitle="Approvisionnement de l’application web avec le cache Redis" 
	description="Utilisez un modèle Azure Resource Manager pour déployer une application web Azure avec le cache Redis." 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="tomfitz"/>

# Création d’une application web avec le cache Redis à l’aide d’un modèle

Dans cette rubrique, vous allez apprendre à créer un modèle Azure Resource Manager qui déploie une application web Azure avec le cache Redis. Vous allez apprendre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins.

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

Pour le modèle complet, consultez la page [Modèle d’application web avec le cache Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## Ce que vous allez déployer

Dans ce modèle, vous allez déployer :

- Application web Azure
- Cache Redis Azure.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## Paramètres à spécifier

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[AZURE.INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]



## Ressources à déployer

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### Cache Redis

Crée le cache Redis Azure utilisé avec l'application web. Le nom du cache est spécifié dans le paramètre **redisCacheName**.

Le modèle crée le cache dans le même emplacement que l’application web, ce qui est recommandé pour de meilleures performances.

    {
      "apiVersion": "2014-04-01-preview",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('siteLocation')]",
      "properties": {
        "sku": {
          "name": "[parameters('redisCacheSKU')]",
          "family": "[parameters('redisCacheFamily')]",
          "capacity": "[parameters('redisCacheCapacity')]"
        },
        "redisVersion": "[parameters('redisCacheVersion')]",
        "enableNonSslPort": true
      }
    }

### Application web

Crée l’application web avec le nom spécifié dans le paramètre **siteName**.

Notez que l’application web est configurée selon des paramètres qui lui permettent d’utiliser le cache Redis. Ces paramètres sont créés de façon dynamique en fonction des valeurs fournies lors du déploiement.
        
    {
      "apiVersion": "2015-04-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[parameters('siteLocation')]",
      "dependsOn": [
          "[resourceId('Microsoft.Web/serverFarms', parameters('hostingPlanName'))]",
          "[resourceId('Microsoft.Cache/Redis', parameters('redisCacheName'))]"
      ],
      "properties": {
          "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
          {
              "apiVersion": "2015-06-01",
              "type": "config",
              "name": "web",
              "dependsOn": [
                  "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
              ],
              "properties": {
                  "appSettings": [
                      {
                          "name": "REDIS_HOST",
                          "value": "[concat(parameters('siteName'), '.redis.cache.windows.net:6379')]"
                      },
                      {
                          "name": "REDIS_KEY",
                          "value": "[listKeys(resourceId('Microsoft.Cache/Redis', parameters('redisCacheName')), '2014-04-01').primaryKey]"
                      }
                  ]
              }
          }
      ]
    }



## Commandes pour l’exécution du déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Interface de ligne de commande Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---------HONumber=AcomDC_0309_2016-->