---
title: Surveillance des performances des applications mobiles - Azure Application Insights | Microsoft Docs
description: "Analyse de l’utilisation et des performances des applications pour les développeurs d’applications mobiles. DevOps avec HockeyApp et Application Insights."
author: alancameronwills
services: application-insights
documentationcenter: 
manager: douge
ms.assetid: 93e0f108-9605-4d8b-8fce-512bfe8c3f0f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: c4077aa88a401a1c75c0d3fbcd58186f7cb137c2


---
# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>Analyse mobile avec Application Insights et HockeyApp
Analysez les performances et l’utilisation de vos applications de test bêta et de vos applications mobiles et de bureau déployées avec [HockeyApp](https://hockeyapp.net/). Analysez les applications principales et de service web avec [Azure Application Insights](app-insights-overview.md). Analysez les données des applications clientes et serveur dans Analytics, et affichez les graphiques les uns à côté des autres dans un tableau de bord Azure.

Microsoft Developer Analytics propose deux solutions pour l’analyse des performances d’applications :

* **HockeyApp** pour les applications mobiles et de bureau :
  * distribution des versions de test aux utilisateurs sélectionnés ;
  * analyse des incidents ;
  * données de télémétrie personnalisées pour l’analyse de l’utilisation.
* **Application Insights** pour les applications principales, de sites web et de services web :
  * alertes et métriques de performances et d’utilisation ;
  * rapports d’exceptions et suivi de diagnostic ;
  * recherche de diagnostic avec des filtres et des liens de télémétrie associés.

Les deux solutions proposent :

* un puissant **[langage de requête analytique](app-insights-analytics.md)** pour les diagnostics et l’analyse ;
* **[l’exportation des données](app-insights-export-telemetry.md)** vers votre système de stockage ;
* un affichage de graphiques et de tableaux analytiques dans le **tableau de bord intégré**.

## <a name="monitor-your-app-components"></a>Analyser les composants de votre application
Suivez les instructions contenues dans ces pages pour installer le Kit de développement logiciel (SDK) dans votre code et commencer le suivi de votre application.

### <a name="web-apps---application-insights"></a>Applications web - Application Insights
* [Application web ASP.NET](app-insights-asp-net.md) 
* [Applications web Java](app-insights-java-get-started.md)
* [Application web Node.js](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Azure Cloud Services](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>Applications mobiles - HockeyApp
* [Application iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Application Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Application Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Application Windows universelle](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Application Windows Phone 8 et 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Application Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Pour les applications de bureau Windows, nous vous recommandons HockeyApp, mais vous pouvez également [envoyer les données de télémétrie à Application Insights à partir d’une application Windows](app-insights-windows-desktop.md). Vous souhaiterez peut-être en faire l’expérience avec Application Insights.

## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>Analyse et exportation de données de télémétrie HockeyApp
Vous pouvez examiner les données de télémétrie personnalisées et de journalisation HockeyApp à l’aide des fonctionnalités Analyse et Exportation continue d’Application Insights en [configurant un pont](app-insights-hockeyapp-bridge-app.md).




<!--HONumber=Jan17_HO4-->


