---
title: "Prise en charge par Azure Application Insights de plusieurs rôles, microservices et conteneurs | Microsoft Docs"
description: "Surveillance des performances et de l’utilisation des applications constituées de plusieurs composants ou de plusieurs rôles."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: dad17277452081427a01d077128a3a137b2190f5
ms.contentlocale: fr-fr
ms.lasthandoff: 05/17/2017


---
# <a name="monitor-multi-role-applications-with-application-insights-preview"></a>Surveiller des applications multirôles avec Application Insights (préversion)

Vous pouvez surveiller des applications constituées de plusieurs composants, rôles ou services avec [Azure Application Insights](app-insights-overview.md). L’intégrité des rôles et des relations entre eux sont affichés dans un même plan d’application. Vous pouvez suivre des opérations individuelles à travers plusieurs rôles avec une corrélation HTTP automatique. Vous pouvez intégrer et corréler les diagnostics des conteneurs à la télémétrie de l’application. Utilisez une seule ressource Application Insights pour tous les rôles de votre application. 

![Plan d’une application multirôle](./media/app-insights-monitor-multi-role-apps/app-map.png)

Nous utilisons ici « rôle » au sens large pour désigner tout composant ou service d’une application qui est généré sous la forme d’un projet distinct. Par exemple, une application métier classique peut se composer de plusieurs rôles qui communiquent via une API REST. Les rôles peuvent être hébergés dans des conteneurs, comme Docker ou Service Fabric, ou sur des hôtes locaux ou cloud. 

### <a name="sharing-a-single-application-insights-resource"></a>Partage d’une seule ressource Application Insights 

La technique consiste principalement à envoyer la télémétrie à partir de chaque rôle de votre application vers la même ressource Application Insights, tout en utilisant la propriété `cloud_RoleName` pour distinguer les rôles quand c’est nécessaire. 

Dans certains cas, vous ne pourrez peut-être pas l’employer et vous préférerez alors utiliser des ressources distinctes pour différents groupes de rôles. Par exemple, vous aurez peut-être besoin de ressources différentes pour la gestion ou la facturation. Si vous utilisez des ressources distinctes, vous ne voyez pas tous les rôles affichés sur un même plan d’application et vous ne pouvez pas interroger les différents rôles dans l’[analytique](app-insights-analytics.md). Vous devez également configurer les ressources distinctes.

Cela étant, nous supposons pour le reste de ce document que vous voulez envoyer les données de plusieurs rôles vers une même ressource Application Insights.

## <a name="configure-multi-role-applications"></a>Configurer les applications multirôles

Pour obtenir le plan d’une application multirôle, voici ce que vous devez faire :

* **Installez la version préliminaire la plus récente**  du package Application Insights dans chaque rôle de l’application. 
* **Partagez une même ressource Application Insights** pour tous les rôles de votre application.
* **Activez Plan d’application multirôle** dans le panneau Préversions.

Configurez chaque rôle de votre application en utilisant la méthode qui convient à son type. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Installer la version préliminaire la plus récente du package

Mettez à jour ou installez les packages Application Insights dans le projet pour chaque rôle. Si vous utilisez Visual Studio :

1. Cliquez avec le bouton droit sur un projet et sélectionnez **Gérer les packages NuGet**. 
2. Sélectionnez **Inclure la version préliminaire**.
3. Si des packages Application Insights apparaissent dans les mises à jour, sélectionnez-les. 

    Sinon, recherchez et installez le package approprié :
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric : pour les rôles s’exécutant en tant qu’exécutables invités et les conteneurs Docker s’exécutant dans une application Service Fabric
    * Microsoft.ApplicationInsights.ServiceFabric.Native : pour des services fiables dans les applications Service Fabric
    * Microsoft.ApplicationInsights.Kubernetes : pour les rôles s’exécutant dans Docker sur Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Partager une même ressource Application Insights

* Dans Visual Studio, cliquez avec le bouton droit sur un projet et sélectionnez **Configurer Application Insights** ou **Application Insights > Configurer**. Pour le premier projet, utilisez l’Assistant pour créer une ressource Application Insights. Pour les projets suivants, sélectionnez la même ressource.
* S’il n’existe aucun menu Application Insights, configurez-le manuellement :

   1. Dans le [portail Azure](https://portal,azure.com), ouvrez la ressource Application Insights que vous avez déjà créée pour un autre rôle.
   2. Dans le panneau Vue d’ensemble, ouvrez la liste déroulante Bases et sélectionnez la **clé d’instrumentation**.
   3. Dans votre projet, ouvrez ApplicationInsights.config et insérez : `<InstrumentationKey>your copied key</InstrumentationKey>`

![Copiez la clé d’instrumentation dans le fichier .config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3. Activer Plan d’une application multirôle

Dans le portail Azure, ouvrez la ressource pour votre application. Dans le panneau Préversions, activez *Plan d’application multirôle*.

### <a name="4-enable-docker-metrics-optional"></a>4. Activer les métriques Docker (facultatif) 

Si un rôle s’exécute dans un Docker hébergé sur une machine virtuelle Windows Azure, vous pouvez collecter des métriques supplémentaires auprès du conteneur. Dans votre fichier de configuration [Azure Diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md), insérez ceci :

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-roles"></a>Utilisez cloud_RoleName pour séparer les rôles

La propriété `cloud_RoleName` est attachée à toute la télémétrie. Elle identifie le rôle ou le service dont provient la télémétrie. (Elle n’est pas identique à cloud_RoleInstance, qui sépare les rôles identiques qui s’exécutent en parallèle sur plusieurs processus serveur ou sur plusieurs ordinateurs.)

Dans le portail, vous pouvez filtrer ou segmenter votre télémétrie à l’aide de cette propriété. Dans cet exemple, le panneau Échecs est filtré pour afficher seulement les informations du service web frontal, en éliminant les échecs du back-end de l’API du CRM :

![Graphique des métriques segmentées par nom de rôle cloud](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-roles"></a>Suivre les opérations entre les rôles

Vous pouvez suivre les appels d’un service à un autre service effectués lors du traitement d’une opération individuelle.


![Afficher la télémétrie pour une opération](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Cliquez pour obtenir une liste corrélée de la télémétrie pour cette opération entre le serveur web frontal et l’API principale :

![Rechercher dans les rôles](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Étapes suivantes

* [Télémétrie distincte entre le développement, les tests et la production](app-insights-separate-resources.md)

