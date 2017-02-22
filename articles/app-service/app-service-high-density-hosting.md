---
title: "Hébergement haute densité sur Azure App Service | Microsoft Docs"
description: "Hébergement haute densité sur Azure App Service"
author: btardif
manager: wpickett
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/11/2017
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 0c2677b388f7a88ff88715a05212633565393cc2
ms.openlocfilehash: 2d5d1d5123ca718b2e7dcdf426b77f91969dc9dc


---
# <a name="high-density-hosting-on-azure-app-service"></a>Hébergement haute densité sur Azure App Service
Lorsque vous utilisez App Service, votre application est découplée à partir de la capacité allouée par deux concepts :

* **L’Application :** représente l’application et sa configuration lors de l’exécution. Par exemple, elle inclut la version de .NET que le runtime doit charger, les paramètres de l’application, etc.
* **Le plan App Service :** définit les caractéristiques de capacité, l’ensemble des fonctionnalités disponibles et la localité de l’application. Par exemple, les caractéristiques peuvent être : machines puissantes (quatre cœurs), quatre instances, fonctionnalités Premium pour les États-Unis de l’Est.

Une application est toujours liée à un plan App Service, mais un Plan App Service peut fournir de la capacité à une ou plusieurs applications.

Par conséquent, la plateforme fournit la flexibilité nécessaire pour isoler une application unique, ou partager les ressources entre plusieurs applications avec un plan App Service commun.

Toutefois, lorsque plusieurs applications partagent un plan App Service, une instance de cette application sera exécutée sur chaque instance de ce plan App Service.

## <a name="per-app-scaling"></a>Mise à l’échelle par application
*La mise à l’échelle par application* est une fonction qui peut être activée au niveau du plan App Service et activée par chaque application.

La mise à l’échelle par application permet de mettre à l’échelle une application indépendamment du plan App Service qui l’héberge. De cette façon, un plan App Service peut être configuré pour fournir 10 instances, mais une application peut être définie pour être mise l’échelle pour seulement 5 d’entre elles.

   >[!NOTE]
   >La mise à l’échelle par application est disponible uniquement pour les plans App Service **Premium** de référence
   >

### <a name="per-app-scaling-using-powershell"></a>Mise à l’échelle par application à l’aide de PowerShell

Vous pouvez créer un plan configuré comme plan de *mise à l’échelle par application* en passant l’attribut ```-perSiteScaling $true``` dans l’applet de commande ```New-AzureRmAppServicePlan```.

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Si vous souhaitez mettre à jour un plan App Service existant pour utiliser cette fonctionnalité : 

- Obtenez le plan cible ```Get-AzureRmAppServicePlan```
- Modifiez la propriété localement ```$newASP.PerSiteScaling = $true```
- Validez les modifications vers Azure ```Set-AzureRmAppServicePlan``` 

```
    # Get the new App Service Plan and modify the "PerSiteScaling" property.
    $newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
    $newASP

    #Modify the local copy to use "PerSiteScaling" property.
    $newASP.PerSiteScaling = $true
    $newASP
    
    #Post updated app service plan back to azure
    Set-AzureRmAppServicePlan $newASP
```

Une fois qu’un plan a été configuré, vous pouvez définir le nombre maximal d’instances de chacune des applications.

Dans l’exemple ci-dessous, l’application est limitée à deux instances maximum quel que soit le nombre d’instances mis à l’échelle pour le plan App Service sous-jacent.

```
    # Get the app we want to configure to use "PerSiteScaling"
    $newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
    # Modify the NumberOfWorkers setting to the desired value.
    $newapp.SiteConfig.NumberOfWorkers = 2
    
    # Post updated app back to azure
    Set-AzureRmWebApp $newapp
```

### <a name="per-app-scaling-using-azure-resource-manager"></a>Mise à l’échelle par application avec Azure Resource Manager

Le *modèle Azure Resource Manager* ci-dessous crée les éléments suivants :

- Un plan App Service réparti sur 10 instances
- Une application configurée pour une mise à l’échelle vers un maximum de cinq instances.

Le plan App Service définit la propriété **PerSiteScaling** sur true ```"perSiteScaling": true```. L’application définit le **nombre de Workers** à utiliser sur 5 ```"properties": { "numberOfWorkers": "5" }```.

```
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
                "name": "P1",
                "tier": "Premium",
                "size": "P1",
                "family": "P",
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
                    "properties": { "numberOfWorkers": "5" }
             } ]
         }]
    }
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Configuration recommandée pour l’hébergement haute densité
La fonctionnalité de mise à l’échelle par application est activée dans les régions Azure et les environnements App Service publics. Toutefois, la stratégie recommandée consiste à utiliser des environnements App Service pour tirer parti de leurs fonctionnalités avancées et des pools de capacité supérieure.  

Suivez ces étapes pour configurer un hébergement haute densité pour vos applications :

1. Configurez l’environnement App Service et choisissez un pool de Workers consacré au scénario d’hébergement haute densité.
2. Créez un seul plan App Service et mettez-le à l’échelle pour utiliser toute la capacité disponible sur le pool de Workers.
3. Définissez l’indicateur PerSiteScaling sur true dans le plan App Service.
4. Les nouvelles applications sont créées et attribuées à ce plan App Service avec la propriété **numberOfWorkers** définie sur **1**. Cela donne la densité la plus élevée possible sur ce pool de Workers.
5. Le nombre de Workers peut être configuré indépendamment par application pour accorder des ressources supplémentaires en fonction des besoins. Par exemple, une application à utilisation élevée peut définir **numberOfWorkers** sur **3** afin d’avoir plus de capacité de traitement pour cette application, tandis que les applications à faible utilisation peuvent définir **numberOfWorkers** sur **1**.




<!--HONumber=Jan17_HO2-->


