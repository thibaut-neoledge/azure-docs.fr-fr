<properties 
	pageTitle="Hébergement haute densité sur Azure App Service" 
	description="Hébergement haute densité sur Azure App Service" 
	authors="btardif" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/07/2016" 
	ms.author="byvinyal"/>

# Hébergement haute densité sur Azure App Service#

## Présentation de la mise à l’échelle des applications##

Lorsque vous utilisez App Service, votre application sera découplée à partir de la capacité allouée par 2 concepts :
 
>**L’Application :** représente l’application et sa configuration lors de l’exécution. Par exemple, la version de .NET chargée lors de l’exécution, les paramètres d’application, etc.

>**Le plan App Service :** définit les caractéristiques de capacité, l’ensemble des fonctionnalités disponibles et la localité de l’application. Par exemple Grande machine (4 cœurs), 4 instances, fonctionnalités premium dans l’Est des États-Unis

Une application est toujours liée à un **Plan App Service**, mais un **Plan App Service** peut fournir de la capacité à une ou plusieurs applications.

Cela signifie que la plateforme fournit la flexibilité nécessaire pour isoler une application unique, ou de partager les ressources entre plusieurs applications avec un **Plan App Service** commun.

Toutefois, lorsque plusieurs applications partagent un **Plan App Service**, une instance de cette application sera exécutée sur chaque instance de ce **Plan App Service**.

## Mise à l’échelle par application##
**La mise à l’échelle par application** est une fonction qui peut être activée au niveau **Plan App Service** niveau et activée par chaque application.

**La mise à l’échelle par application** vous permet de mettre à l’échelle une application indépendamment du **Plan App Service** utilisé pour l’héberger. De cette façon, un **Plan App Service** peut être configuré pour fournir 10 instances, mais une application peut être définie pour être mise l’échelle pour seulement 5 d'entre elles.

Le modèle ARM suivant créera un **Plan App Service** mis à l’échelle jusqu'à 10 instances et une application configurée pour utiliser la **Mise à l’échelle par application** avec une mise à l’échelle jusqu’à 5 instances seulement.

Pour ce faire, le Plan App Service définit la propriété de **mise à l’échelle de site** sur true (`"perSiteScaling": true` ) et l’application définit le **nombre de threads de Workers** à utiliser pour 1 `"properties": { "numberOfWorkers": "1" }`

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true 
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "1" }
             } ]
         }]
    }


## Configuration recommandée pour l’hébergement haute densité

**La mise à l’échelle par application** est une fonctionnalité qui est activée dans les régions Azure publiques ainsi que dans les environnements App Service, mais la stratégie recommandée consiste à utiliser des environnements App Service pour utiliser leurs fonctionnalités avancées et leurs pools de capacité supérieurs.

Suivez les étapes répertoriées ci-dessous comme indication sur la configuration de **l’Hébergement haute densité** pour vos applications.

1. Configurez l**’Environnement App Service** et choisissez un **Pool de Workers** qui sera consacré au scénario d*’Hébergement haute densité*.

1. Créez un seul **Plan App Service** et mettez-le à l’échelle pour utiliser toute la capacité disponible sur le **Pool de Workers**.

1. Définissez l’indicateur de mise à l’échelle de site sur true dans le **Plan App Service**.

1. De nouveaux sites sont créés et affectés à ce **Plan App Service** avec la propriété *numberOfWorkers* définie sur *1*. Cela donne la densité la plus élevée possible sur ce **Pool de Workers**

1. Le nombre de Workers peut être configuré indépendamment par site, pour accorder des ressources supplémentaires en fonction des besoins. Par exemple, un site à utilisation élevée peut définir *numberOfWorkers* sur *3* afin d’avoir plus de capacité de traitement pour cette application, tandis que les sites à faible utilisation peuvent définir *numberOfWorkers* sur *1*.

<!---HONumber=AcomDC_0810_2016-->