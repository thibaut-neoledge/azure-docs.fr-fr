---
title: "Débogueur de captures instantanées Azure Application Insights pour les applications .NET | Microsoft Docs"
description: "Déboguer des captures instantanées collectées automatiquement lorsque des exceptions sont levées dans les applications de production .NET"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/30/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 94b073df00b690f1347e246eeb7e12fdd1bd7cc1
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Déboguer des captures instantanées sur les exceptions dans les applications .NET

*Cette fonctionnalité est en version préliminaire.*

Collecter automatiquement un instantané de débogage à partir de votre application web dynamique lorsqu’une exception se produit. L’instantané indique l’état du code source et des variables au moment où l’exception a été levée. Le débogueur de captures instantanées d’[Application Insights](app-insights-overview.md) analyse la télémétrie des exceptions à partir de votre application web. Il collecte des captures instantanées sur les principales exceptions générées afin que vous disposiez des informations dont vous avez besoin pour diagnostiquer les problèmes de production. Intégrez le [package NuGet de collecte des captures instantanées](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) à votre application et configurez éventuellement les paramètres de collecte dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Les captures instantanées apparaissent sur les [exceptions](app-insights-asp-net-exceptions.md) dans le portail Application Insights.

Vous pouvez afficher les captures instantanées de débogage dans le portail pour consulter la pile des appels et inspecter les variables à chaque frame de pile d’appel. Pour améliorer la puissance de débogage du code source, ouvrez les captures instantanées à l’aide de Visual Studio 2017 Enterprise en [téléchargeant l’extension du débogueur de capture instantanée pour Visual Studio](https://aka.ms/snapshotdebugger).

La collection de captures instantanées est disponible pour les applications web ASP.NET en cours d’exécution sur le .NET Framework 4.6 et versions ultérieures, hébergé sur IIS dans Calcul Azure ou Azure App Service.

## <a name="configure-snapshot-collection"></a>Configurer la collecte de captures instantanées

1. Si vous ne l’avez pas encore fait, [configurez Application Insights dans votre application web](app-insights-asp-net.md).

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application. 

3. Passez en revue les options par défaut ajoutées à [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) par le package.

```xml
  <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
      <IsEnabled>true</IsEnabled>
      <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
      <!-- DeveloperMode is a property on the active TelemetryChannel. -->
      <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
      <!-- How many times we need to see an exception before we ask for snapshots. -->
      <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
      <!-- The maximum number of examples we create for a single problem. -->
      <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
      <!-- The maximum number of problems that we can be tracking at any time. -->
      <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
      <!-- How often to reset problem counters. -->
      <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
      <!-- The maximum number of snapshots allowed in one minute. -->
      <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
      <!-- The maximum number of snapshots allowed per day. -->
      <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
    </Add>
  </TelemetryProcessors>
```

Les captures instantanées ne sont collectées que sur les exceptions visibles pour Application Insights. Dans certains cas, vous devrez peut-être [configurer la collection d’exceptions](app-insights-asp-net-exceptions.md#exceptions) pour afficher les exceptions dont les captures instantanées figurent sur le portail.

## <a name="debugging-snapshots-in-the-application-insights-portal"></a>Débogage de captures instantanées dans le portail Application Insights

Si une capture instantanée est disponible pour une exception ou un ID de problème donné, un lien *Ouvrir la capture instantanée de débogage* s’affiche sur l’[exception](app-insights-asp-net-exceptions.md) dans le portail Application Insights.

![Bouton Ouvrir la capture instantanée de débogage sur l’exception](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

Une pile d’appel et un volet de variables s’affichent dans la vue Capture instantanée de débogage. Sélectionnez des images de la pile d’appel dans le volet correspondant pour afficher les variables et paramètres locaux de cet appel de fonction dans le volet des variables.

![Afficher la capture instantanée de débogage dans le portail](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Les captures instantanées peuvent contenir des informations sensibles et ,par défaut, ne sont pas visibles. Pour afficher les captures instantanées, le rôle `Application Insights Snapshot Debugger` doit vous être affecté dans le portail de l’abonnement ou de la ressource. Actuellement, ce rôle peut être affecté seulement par les propriétaires d’abonnements par utilisateur. L’affectation du rôle à des groupes Azure Active Directory n’est actuellement pas pris en charge.

## <a name="debugging-snapshots-with-visual-studio-2017-enterprise"></a>Déboguer des captures instantanées avec Visual Studio 2017 Enterprise
Vous pouvez cliquer sur le bouton *Télécharger la capture instantanée* pour télécharger un fichier `.diagsession`, qui peut être ouvert par Visual Studio 2017 Enterprise. L’ouverture du fichier `.diagsession` exige actuellement que vous commenciez par [télécharger et installer l’extension du débogueur de captures instantanées pour Visual Studio](https://aka.ms/snapshotdebugger).

Après avoir ouvert le fichier de capture instantanée, vous accédez à la page débogage Minidump de Visual Studio, où vous pouvez commencer le débogage de la capture instantanée en cliquant sur *Déboguer le code géré*. Vous accédez à la ligne de code où l’exception a été levée et pouvez déboguer l’état actuel du processus.

![Afficher la capture instantanée de débogage dans Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

La capture instantanée téléchargée contient tous les fichiers de symboles qui ont été détectés sur votre serveur d’applications web. Ces fichiers de symboles sont nécessaires pour associer les données de capture instantanée au code source. Pour les applications Azure App Service, veillez à activer le déploiement des symboles lors de la publication de vos applications web.

## <a name="how-snapshots-work"></a>Fonctionnement des captures instantanées

Au démarrage de votre application, un processus distinct de chargeur de captures instantanées, qui analyse votre application en termes de demandes de captures instantanées, est créé. Lorsqu’une capture instantanée est demandée, un cliché instantané du processus en cours d’exécution est effectué dans les 10 à 20 ms environ. Le processus de cliché instantané est ensuite analysé et une capture instantanée créée sans interrompre le processus principal en cours d’exécution et la desserte du trafic aux utilisateurs. La capture instantanée est ensuite chargée vers Application Insights, ainsi que les fichiers de symboles (.pdb) requis pour son affichage.

## <a name="current-limitations"></a>Limitations actuelles

### <a name="publishing-symbols"></a>Symboles de publication
Le débogueur de capture instantanées requiert la présence des fichiers de symboles sur le serveur de production pour le décodage des variables et pour proposer une expérience de débogage dans Visual Studio. La version 15.2 de Visual Studio 2017 publie des symboles pour les builds de mise en production publiés par défaut lors de la publication dans Azure App Service. Dans les versions antérieures, vous devez ajouter la ligne suivante à votre fichier de profil de publication `.pubxml` afin que les symboles soient publiés en mode Mise en production.

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Pour Calcul Azure les autres types, vérifiez que les fichiers de symboles se trouvent dans le même dossier que celui du .dll de l’application principale (généralement `wwwroot/bin`), ou sont disponibles via le chemin d’accès actuel.

### <a name="optimized-builds"></a>Optimisation des versions
Dans certains cas, les variables locales ne sont pas visibles dans les builds de mise en production en raison des optimisations appliquées pendant le processus de génération. Cette limite sera corrigée dans une prochaine version du package NuGet.

## <a name="next-steps"></a>Étapes suivantes

* [Diagnostiquer des exceptions dans vos applications web](app-insights-asp-net-exceptions.md) explique comment rendre visible à Application Insights un plus grand nombre d’exceptions. 
* [Détection intelligente](app-insights-proactive-diagnostics.md) permet de détecter automatiquement les anomalies relatives aux performances.

