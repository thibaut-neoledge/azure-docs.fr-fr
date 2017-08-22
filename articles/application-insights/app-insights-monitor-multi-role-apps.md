---
title: Prise en charge par Azure Application Insights de plusieurs composants, microservices et conteneurs | Microsoft Docs
description: "Surveillance des performances et de l’utilisation des applications constituées de plusieurs composants ou de plusieurs rôles."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: d8b466caba7201a5bb8612e773ad61943f6d1cf2
ms.contentlocale: fr-fr
ms.lasthandoff: 07/25/2017

---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Surveiller des applications multicomposants avec Application Insights (préversion)

Vous pouvez surveiller des applications constituées de plusieurs composants serveurs, rôles ou services avec [Azure Application Insights](app-insights-overview.md). L’intégrité des composants et des relations entre eux sont affichés dans un même plan d’application. Vous pouvez suivre des opérations individuelles à travers plusieurs composants avec une corrélation HTTP automatique. Vous pouvez intégrer et corréler les diagnostics des conteneurs à la télémétrie de l’application. Utilisez une seule ressource Application Insights pour tous les composants de votre application. 

![Plan d’application multicomposant](./media/app-insights-monitor-multi-role-apps/app-map.png)

Nous utilisons « composant » ici pour signifier une partie fonctionnelle d’une application de grande taille. Par exemple, une application métier classique peut se composer de code client s’exécutant dans des navigateurs web, échangeant avec un ou plusieurs services d’application web, qui à leur tour utilisent des services principaux. Les composants serveur peuvent être hébergés localement ou dans le cloud, être des rôles web et worker Azure, ou s’exécuter dans des conteneurs comme Docker ou Service Fabric. 

### <a name="sharing-a-single-application-insights-resource"></a>Partage d’une seule ressource Application Insights 

La technique principale consiste à envoyer la télémétrie depuis chaque composant de votre application à la même ressource Application Insights, mais à utiliser la propriété `cloud_RoleName` pour distinguer les composants quand c’est nécessaire. Le SDK Application Insights ajoute la propriété `cloud_RoleName` aux données de télémétrie émises par les composants. Par exemple, le SDK ajoute un nom de site web ou un nom de rôle de service à la propriété `cloud_RoleName`. Vous pouvez remplacer cette valeur par un telemetryinitializer. Le plan d’application utilise la propriété `cloud_RoleName` pour identifier les composants sur le plan.

Pour plus d’informations sur la façon de remplacer la propriété `cloud_RoleName`, consultez [Ajouter des propriétés : ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

Dans certains cas, ceci peut ne pas être approprié et vous pouvez alors préférer utiliser des ressources distinctes pour les différents groupes de composants. Par exemple, vous aurez peut-être besoin de ressources différentes pour la gestion ou la facturation. Si vous utilisez des ressources distinctes, vous ne voyez pas tous les composants affichés sur un même plan d’application et vous ne pouvez pas interroger les différents composants dans l’[analytique](app-insights-analytics.md). Vous devez également configurer les ressources distinctes.

Cela étant, nous supposons pour le reste de ce document que vous voulez envoyer les données de plusieurs composants vers une même ressource Application Insights.

## <a name="configure-multi-component-applications"></a>Configurer des applications multicomposants

Pour obtenir le plan d’une application multicomposant, voici ce que vous devez faire :

* **Installez la préversion la plus récente** du package Application Insights dans chaque composant de l’application. 
* **Partagez une même ressource Application Insights** pour tous les composants de votre application.
* **Activez Plan d’application multirôle** dans le panneau Préversions.

Configurez chaque composant de votre application en utilisant la méthode qui convient à son type. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Installer la préversion la plus récente du package

Mettez à jour ou installez les packages Application Insights dans le projet pour chaque composant serveur. Si vous utilisez Visual Studio :

1. Cliquez avec le bouton droit sur un projet et sélectionnez **Gérer les packages NuGet**. 
2. Sélectionnez **Inclure la préversion**.
3. Si des packages Application Insights apparaissent dans les mises à jour, sélectionnez-les. 

    Sinon, recherchez et installez le package approprié :
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric : pour les composants s’exécutant en tant qu’exécutables invités et les conteneurs Docker s’exécutant dans une application Service Fabric
    * Microsoft.ApplicationInsights.ServiceFabric.Native : pour des services fiables dans les applications Service Fabric
    * Microsoft.ApplicationInsights.Kubernetes : pour les composants s’exécutant dans Docker sur Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Partager une même ressource Application Insights

* Dans Visual Studio, cliquez avec le bouton droit sur un projet et sélectionnez **Configurer Application Insights** ou **Application Insights > Configurer**. Pour le premier projet, utilisez l’Assistant pour créer une ressource Application Insights. Pour les projets suivants, sélectionnez la même ressource.
* S’il n’existe aucun menu Application Insights, configurez-le manuellement :

   1. Dans le [portail Azure](https://portal,azure.com), ouvrez la ressource Application Insights que vous avez déjà créée pour un autre composant.
   2. Dans le panneau Vue d’ensemble, ouvrez la liste déroulante Bases et sélectionnez la **clé d’instrumentation**.
   3. Dans votre projet, ouvrez ApplicationInsights.config et insérez : `<InstrumentationKey>your copied key</InstrumentationKey>`

![Copiez la clé d’instrumentation dans le fichier .config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3. Activer Plan d’une application multirôle

Dans le portail Azure, ouvrez la ressource pour votre application. Dans le panneau Préversions, activez *Plan d’application multirôle*.

### <a name="4-enable-docker-metrics-optional"></a>4. Activer les métriques Docker (facultatif) 

Si un composant s’exécute dans un Docker hébergé sur une machine virtuelle Windows Azure, vous pouvez collecter des métriques supplémentaires auprès du conteneur. Dans votre fichier de configuration [Azure Diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md), insérez ceci :

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

## <a name="use-cloudrolename-to-separate-components"></a>Utilisez cloud_RoleName pour séparer les composants

La propriété `cloud_RoleName` est attachée à toute la télémétrie. Elle identifie le composant - le rôle ou le service - dont provient la télémétrie. (Elle n’est pas identique à cloud_RoleInstance, qui sépare les rôles identiques qui s’exécutent en parallèle sur plusieurs processus serveur ou sur plusieurs ordinateurs.)

Dans le portail, vous pouvez filtrer ou segmenter votre télémétrie à l’aide de cette propriété. Dans cet exemple, le panneau Échecs est filtré pour afficher seulement les informations du service web frontend, en éliminant les échecs du backend de l’API CRM :

![Graphique des métriques segmentées par nom de rôle cloud](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Suivre les opérations entre les composants

Vous pouvez suivre les appels d’un composant à un autre effectués lors du traitement d’une opération individuelle.


![Afficher la télémétrie pour une opération](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Cliquez pour obtenir une liste corrélée de la télémétrie pour cette opération entre le serveur web frontend et l’API backend :

![Rechercher dans l’ensemble des composants](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Étapes suivantes

* [Télémétrie distincte entre le développement, les tests et la production](app-insights-separate-resources.md)

