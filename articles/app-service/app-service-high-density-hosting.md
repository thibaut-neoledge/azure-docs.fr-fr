---
title: Hébergement haute densité sur Azure App Service | Microsoft Docs
description: Hébergement haute densité sur Azure App Service
author: btardif
manager: wpickett
editor: ''
services: app-service\web
documentationcenter: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/07/2016
ms.author: byvinyal

---
# Hébergement haute densité sur Azure App Service
Lorsque vous utilisez App Service, votre application sera découplée à partir de la capacité allouée par 2 concepts :

* **L’Application :** représente l’application et sa configuration lors de l’exécution. Par exemple, elle inclut la version de .NET que le runtime doit charger, les paramètres de l’application, etc.
* **Le plan App Service :** définit les caractéristiques de capacité, l’ensemble des fonctionnalités disponibles et la localité de l’application. Par exemple, les caractéristiques peuvent être : machines puissantes (quatre cœurs), quatre instances, fonctionnalités Premium pour les États-Unis de l’Est.

Une application est toujours liée à un plan App Service, mais un Plan App Service peut fournir de la capacité à une ou plusieurs applications.

Cela signifie que la plateforme fournit la flexibilité nécessaire pour isoler une application unique, ou partager les ressources entre plusieurs applications avec un plan App Service commun.

Toutefois, lorsque plusieurs applications partagent un plan App Service, une instance de cette application sera exécutée sur chaque instance de ce plan App Service.

## Mise à l’échelle par application
*La mise à l’échelle par application* est une fonction qui peut être activée au niveau du plan App Service et activée par chaque application.

La mise à l’échelle par application permet de mettre à l’échelle une application indépendamment du plan App Service qui l’héberge. De cette façon, un plan App Service peut être configuré pour fournir 10 instances, mais une application peut être définie pour être mise l’échelle pour seulement 5 d’entre elles.

Le modèle Azure Resource Manager suivant crée un plan App Service mis à l’échelle sur 10 instances et une application configurée pour utiliser la mise à l’échelle par application uniquement sur 5 instances.

Pour ce faire, le plan App Service définit la propriété de **mise à l’échelle de site** sur true (`"perSiteScaling": true`) et l’application définit le **nombre de threads de Workers** à utiliser sur 1 (`"properties": { "numberOfWorkers": "1" }`).

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
La fonctionnalité de mise à l’échelle par application est activée dans les régions Azure et les environnements App Service publics. Toutefois, la stratégie recommandée consiste à utiliser des environnements App Service pour tirer parti de leurs fonctionnalités avancées et des pools de capacité supérieure.

Suivez ces étapes pour configurer un hébergement haute densité pour vos applications :

1. Configurez l’environnement App Service et choisissez un pool de Workers qui sera consacré au scénario d’hébergement haute densité.
2. Créez un seul plan App Service et mettez-le à l’échelle pour utiliser toute la capacité disponible sur le pool de Workers.
3. Définissez l’indicateur de mise à l’échelle de site sur true dans le plan App Service.
4. De nouveaux sites sont créés et affectés à ce plan App Service avec la propriété **numberOfWorkers** définie sur **1**. Cela donne la densité la plus élevée possible sur ce pool de Workers.
5. Le nombre de Workers peut être configuré indépendamment par site, pour accorder des ressources supplémentaires en fonction des besoins. Par exemple, un site à utilisation élevée peut définir **numberOfWorkers** sur **3** afin d’avoir plus de capacité de traitement pour cette application, tandis que les sites à faible utilisation peuvent définir **numberOfWorkers** sur **1**.

<!---HONumber=AcomDC_0907_2016-->