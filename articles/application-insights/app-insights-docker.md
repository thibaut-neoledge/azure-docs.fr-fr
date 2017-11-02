---
title: "Surveiller les applications Docker dans Azure Application Insights | Microsoft Docs"
description: "Vous pouvez visualiser les compteurs de performances, les événements et les exceptions Docker dans Application Insights, avec les données de télémétrie des applications en conteneur."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 68f408f18b767ed9c5aba8fed8c97f021cdeb123
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-docker-applications-in-application-insights"></a>Analyse des applications Docker dans Application Insights
Les événements de cycle de vie et les compteurs de performances provenant de conteneurs [Docker](https://www.docker.com/) peuvent être représentés dans Application Insights. Installez l'image [Application Insights](app-insights-overview.md) dans un conteneur de votre hôte pour afficher les compteurs de performances de l'hôte, ainsi que d'autres images.

Avec Docker, vous distribuez vos applications dans des conteneurs légers avec toutes les dépendances. Elles s’exécuteront sur n’importe quelle machine hôte exécutant un moteur Docker.

Lorsque vous exécutez l’[image Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) sur l’hôte Docker, vous bénéficiez des avantages suivants :

* Télémétrie de cycle de vie de tous les conteneurs en cours d'exécution sur l'hôte : démarrage, arrêt, et ainsi de suite.
* Compteurs de performance pour tous les conteneurs. Processeur, mémoire, utilisation du réseau, et bien plus encore.
* Si vous avez [installé le SDK Application Insights pour Java](app-insights-java-live.md) dans les applications en cours d’exécution dans les conteneurs, toutes les données de télémétrie de ces applications auront des propriétés supplémentaires identifiant l’ordinateur hôte et le conteneur. Par exemple, si vous avez des instances d’une application en cours d’exécution dans plusieurs hôtes, vous pouvez facilement filtrer la télémétrie d’application par hôte.

![exemple](./media/app-insights-docker/00.png)

## <a name="set-up-your-application-insights-resource"></a>Configuration de votre ressource Application Insights
1. Connectez-vous au [portail Microsoft Azure](https://azure.com) et ouvrez la ressource Application Insights pour votre application ou [créez-en une](app-insights-create-new-resource.md). 
   
    *Quelle ressource dois-je utiliser ?* Si les applications en cours d’exécution sur votre hôte ont été développées par quelqu’un d’autre, vous devez [créer une ressource Application Insights](app-insights-create-new-resource.md). C'est ce qui vous permet d'afficher et d'analyser les données de télémétrie. (Sélectionnez « Général » comme type d’application.)
   
    Toutefois, si vous êtes le développeur des applications, nous espérons que vous avez [ajouté le Kit SDK Application Insights](app-insights-java-live.md) à chacune d'elles. Si en fait elles sont toutes des composants d'une application d'entreprise unique, vous pouvez toutes les configurer pour envoyer la télémétrie à une ressource, puis vous utiliserez cette même ressource pour afficher les données de performances et du cycle de vie de Docker. 
   
    Un troisième scénario est que vous avez développé la plupart des applications, mais vous utilisez des ressources distinctes pour afficher les données de télémétrie. Dans ce cas, vous pouvez créer une ressource distincte pour les données de Docker. 
2. Ajoutez la mosaïque Docker : choisissez **Ajouter la mosaïque**, faites glisser la mosaïque Docker à partir de la galerie, puis cliquez sur **Terminer**. 
   
    ![exemple](./media/app-insights-docker/03.png)
3. Cliquez sur la liste déroulante **Essentials** et sélectionnez la clé d'instrumentation. Vous l’utilisez pour indiquer au Kit de développement logiciel (SDK) où envoyer ses données de télémétrie.

    ![exemple](./media/app-insights-docker/02-props.png)

Ne fermez pas cette fenêtre de navigateur : vous y reviendrez ultérieurement pour consulter vos données de télémétrie.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Exécution de l'analyse Application Insights sur votre hôte
Maintenant que vous avez défini un emplacement pour l'affichage des données de télémétrie, vous pouvez configurer l'application de conteneur qui les recueillera et les enverra.

1. Connectez-vous à votre hôte Docker. 
2. Modifiez la clé d'instrumentation par cette commande, puis exécutez-la :
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Une seule image Application Insights est requise par hôte Docker. Si votre application est déployée sur plusieurs hôtes Docker, répétez la commande sur chaque hôte.

## <a name="update-your-app"></a>Mise à jour de votre application
Si votre application est instrumentée avec le [SDK Application Insights pour Java](app-insights-java-get-started.md), ajoutez la ligne suivante dans le fichier ApplicationInsights.xml de votre projet, sous l’élément `<TelemetryInitializers>` :

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Ainsi, des informations Docker, telles que le conteneur et l'ID de l'hôte sont ajoutées à chaque élément de télémétrie envoyé à partir de votre application.

## <a name="view-your-telemetry"></a>Affichage de vos données de télémétrie
Revenez à votre ressource Application Insights dans le portail Azure.

Cliquez sur la mosaïque Docker.

Vous verrez bientôt des données arriver à partir de l'application Docker, en particulier si d'autres conteneurs sont en cours d'exécution sur votre moteur Docker.

Voici quelques-uns des affichages que vous pouvez voir.

### <a name="perf-counters-by-host-activity-by-image"></a>Compteurs de performances par hôte, activité par image
![exemple](./media/app-insights-docker/10.png)

![exemple](./media/app-insights-docker/11.png)

Cliquez sur le nom d'un hôte ou d'une image pour plus de détails.

Pour personnaliser l’affichage, cliquez sur un graphique ou sur l’en-tête de la grille, ou utilisez l’option Ajouter un graphique. 

[En savoir plus sur Metrics Explorer](app-insights-metrics-explorer.md).

### <a name="docker-container-events"></a>Événements du conteneur Docker
![exemple](./media/app-insights-docker/13.png)

Pour examiner les événements individuels, cliquez sur [Rechercher](app-insights-diagnostic-search.md). Utilisez le système de recherche et de filtre pour rechercher des événements particuliers. Cliquez sur un événement pour obtenir plus de détails.

### <a name="exceptions-by-container-name"></a>Exceptions par nom de conteneur
![exemple](./media/app-insights-docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Contexte docker ajouté à la télémétrie d'application
Demande de télémétrie envoyée à partir de l'application instrumentée avec le Kit SDK AI, enrichie avec le contexte de Docker :

![exemple](./media/app-insights-docker/16.png)

Temps du processeur et compteurs de performance mémoire disponible, enrichis et regroupés par nom de conteneur Docker :

![exemple](./media/app-insights-docker/15.png)

## <a name="q--a"></a>Questions et réponses
*Quelles sont les fonctionnalités d’Application Insights qui ne sont pas disponibles dans Docker ?*

* Analyse détaillée des compteurs de performances par conteneur et par image.
* Intégration des données de conteneur et d’application dans un tableau de bord.
* [Exportation des données de télémétrie](app-insights-export-telemetry.md) pour approfondir l'analyse vers une base de données, Power BI ou un autre tableau de bord.

*Comment obtenir des données de télémétrie à partir de l’application elle-même ?*

* Installez le Kit SDK Application Insights dans l’application. Découvrez comment faire pour : les [applications web Java](app-insights-java-get-started.md), les [applications web Windows](app-insights-asp-net.md).

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Étapes suivantes

* [Application Insights pour Java](app-insights-java-get-started.md)
* [Application Insights pour Node.js](app-insights-nodejs.md)
* [Application Insights pour ASP.NET](app-insights-asp-net.md)
