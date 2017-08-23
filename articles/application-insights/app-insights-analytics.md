---
title: "Analytics, le puissant outil de recherche d’Azure Application Insights | Microsoft Docs"
description: "Présentation d’Analytics, le puissant outil de recherche d’Application Insights. "
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: a2dc351bd0346f5ca46f1eaafeff678c3339c8c9
ms.contentlocale: fr-fr
ms.lasthandoff: 08/02/2017

---
# <a name="analytics-in-application-insights"></a>Analytics dans Application Insights
[Analytics](app-insights-analytics.md) est la fonctionnalité de recherche performante [d’Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Log Analytics. 

* **[Regardez la vidéo d’introduction](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Testez la version d’évaluation d’Analytics sur nos données simulées](https://analytics.applicationinsights.io/demo)** si votre application n’envoie pas encore de données à Application Insights.
* **[L’aide-mémoire des utilisateurs de SQL](https://aka.ms/sql-analytics)** traduit les idiomes courants.
* **[Référence du langage](app-insights-analytics-reference.md)** Apprenez à utiliser toutes les puissantes fonctionnalités du langage de requête Log Analytics.


## <a name="queries-in-analytics"></a>Requêtes dans Analytics
Une requête classique est une table *source* suivie d’une série *d’opérateurs* séparés par des `|`. 

Par exemple, essayons de découvrir à quelle heure les citoyens de Hyderabad accèdent à notre application web. Et parallèlement, découvrons les codes de résultats qui sont retournés à leurs requêtes HTTP. 

```AIQL
requests
| where timestamp > ago(30d)
| summarize ClientCount = dcount(client_IP) by bin(timestamp, 1h), resultCode
| extend LocalTime = timestamp - 4h
| order by LocalTime desc
| render barchart
```

Nous comptons des adresses IP client distinctes, en les regroupant par heure de la journée sur les sept derniers jours. 

> [!NOTE]
> Pour obtenir des résultats en dehors des dernières 24 heures, incluez explicitement « timestamp » dans votre requête ou utiliser le menu déroulant avec la plage de temps.
>

Affichons les résultats avec la présentation graphique à barres, en choisissant d’empiler les résultats issus de codes de réponse différents :

![Sélectionnez le graphique à barres, les axes x et y, puis la segmentation](./media/app-insights-analytics/020.png)

Il semblerait que notre application soit plus populaire lors de la pause déjeuner et à l’heure du coucher à Hyderabad. (Et nous devrions examiner ces 500 codes).

Il existe également des opérations statistiques puissantes :

![Résultats d’une requête statistique](./media/app-insights-analytics/025.png)

Le langage possède de nombreuses fonctionnalités attrayantes :


* [Filtrer](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) vos données de télémétrie d’application brutes sur tous les champs, y compris les propriétés et métriques personnalisées.
* [Joindre](https://docs.loganalytics.io/queryLanguage/query_language_joinoperator.html) plusieurs tables ; mettez en corrélation les demandes avec les affichages de page, les appels de dépendance, les exceptions et les suivis du journal.
* [Agrégations](https://docs.loganalytics.io/learn/tutorials/aggregations.html)statistiques puissantes.
* Toutes aussi puissantes que SQL, mais beaucoup plus facile pour les requêtes complexes : au lieu d’imbriquer des instructions, vous dirigez les données d’une opération élémentaire à l’autre.
* Visualisations immédiates et puissantes.
* [Épingler des graphiques sur les tableaux de bord Azure](app-insights-analytics-using.md#pin-to-dashboard).
* [Exporter des requêtes dans Power BI](app-insights-analytics-using.md#export-to-power-bi).
* Il existe une [API REST](https://dev.applicationinsights.io/) que vous pouvez utiliser pour exécuter des requêtes par programme, par exemple à partir de PowerShell.


## <a name="connect-to-your-application-insights-data"></a>Connectez-vous à vos données Application Insights
Ouvrez Analytics à partir du [panneau Vue d’ensemble](app-insights-dashboards.md) de votre application dans Application Insights : 

![Ouvrez portal.azure.com, ouvrez votre ressource Application Insights, puis cliquez sur Analyse.](./media/app-insights-analytics/001.png)


## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]



## <a name="query-examples"></a>Exemples de requête

Essayez ces procédures pas à pas pour illustrer la puissance de l’utilisation d’Analytics :

 *  [Diagnostic automatique des pics et sauts d’étape dans les durées des demandes](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Automatic%20diagnostics%20of%20sudden%20spikes%20or%20step%20jumps%20in%20requests%20duration&shared=true)
 *  [Analyse des baisses de performances avec l’analyse de séries chronologiques](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20performance%20degradations%20with%20time%20series%20analysis&shared=true)
 *  [Analyse des défaillances d’application avec le cluster automatique et les modèles différentiels](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20application%20failures%20with%20autocluster%20and%20diffpatterns&shared=true)
 *  [Détections de formes avancées avec l’analyse des séries chronologiques](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Advanced%20shape%20detection%20with%20time%20series%20analysis&shared=true)
 *  [Utilisation d’opérations de fenêtre glissante pour analyser l’utilisation des applications (déploiement MAU/DAU, etc.)](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Using%20sliding%20window%20calculations%20to%20analyze%20usage%20metrics:%20rolling%20MAU~2FDAU%20and%20cohorts&shared=true)
 *  [Détection des interruptions de service en fonction de l’analyse des journaux de débogage](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Detection%20of%20service%20disruptions%20based%20on%20regression%20analysis%20of%20trace%20logs&shared=true) et billet de blog sur le même sujet disponible [ici](https://maximshklar.wordpress.com/2017/02/16/finding-trends-in-traces-with-smart-data-analytics).
 *  [Profilage des performances des applications à l’aide de simples journaux de débogage](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Profiling%20applications'%20performance%20with%20simple%20debug%20logs&shared=true) et billet de blog sur le même sujet disponible [ici](https://yossiattasblog.wordpress.com/2017/03/13/first-blog-post/)
 *  [Mesure de la durée de chaque étape dans votre flux de code à l’aide de simples journaux de débogage](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Measuring%20the%20duration%20of%20each%20step%20in%20your%20code%20flow%20using%20simple%20debug%20logs&shared=true) et billet de blog sur le même sujet disponible [ici](https://yossiattasblog.wordpress.com/2017/03/14/measuring-the-duration-of-each-step-in-your-code-flow-using-simple-debug-logs/)
 *  [Analyse de l’accès concurrentiel à l’aide de simples journaux de débogage](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Analyzing%20concurrency%20with%20simple%20debug%20logs&shared=true) et billet de blog sur le même sujet disponible [ici](https://yossiattasblog.wordpress.com/2017/03/23/analyzing-concurrency-using-simple-debug-logs/)



## <a name="next-steps"></a>Étapes suivantes
* Nous vous recommandons de commencer par la [visite guidée du langage](app-insights-analytics-tour.md). 
* En savoir plus sur [l’utilisation de l’analytique](app-insights-analytics-using.md). 
* [Informations de référence sur le langage](app-insights-analytics-reference.md). 

