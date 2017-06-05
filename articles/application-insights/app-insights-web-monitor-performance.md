---
title: "Surveillez l&quot;intégrité et l&quot;utilisation de votre application avec Application Insights"
description: Prise en main d&quot;Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/25/2015
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: cbdef43381deac957c0e48b7043273c43b032935
ms.contentlocale: fr-fr
ms.lasthandoff: 04/07/2017


---
# <a name="monitor-performance-in-web-applications"></a>Analyse des performances dans les applications web


Assurez-vous que votre application fonctionne correctement et identifiez rapidement toutes les défaillances éventuelles. [Application Insights][start] vous signale tous les problèmes de performances et les exceptions, et vous aide à trouver et à diagnostiquer les causes principales.

Application Insights peut surveiller les services WCF, ainsi que les applications et services web Java et ASP.NET. Ils peuvent être hébergés en local, sur des machines virtuelles, ou en tant que sites web Microsoft Azure. 

Côté client, Application Insights peut récupérer les données télémétriques des pages web et d’un large éventail d’appareils, notamment des applications iOS, Android et Windows Store.

## <a name="setup"></a>Configurer la surveillance des performances
Si vous n'avez pas encore ajouté Application Insights à votre projet (il n'inclut pas ApplicationInsights.config), sélectionnez l'une des options suivantes pour commencer :

* [Applications web ASP.NET](app-insights-asp-net.md)
  * [Ajout de la surveillance des exceptions](app-insights-asp-net-exceptions.md)
  * [Ajout de la surveillance des dépendances](app-insights-monitor-performance-live-website-now.md)
* [Applications web J2EE](app-insights-java-get-started.md)
  * [Ajout de la surveillance des dépendances](app-insights-java-agent.md)

## <a name="view"></a>Exploration des mesures de performances
Sur le [portail Azure](https://portal.azure.com), accédez à la ressource Application Insights que vous avez configurée pour votre application. Le panneau de vue d’ensemble présente les données de performances de base :

Cliquez sur n’importe quel graphique pour afficher plus de détails et davantage de résultats et ce, pendant plus longtemps. Par exemple, cliquez sur la vignette Demandes et sélectionnez une plage de temps :

![Cliquez sur d'autres éléments pour afficher plus de données et sélectionnez une plage de temps](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Cliquez sur un graphique pour choisir les métriques à afficher, ou ajoutez un nouveau graphique et sélectionnez ses métriques :

![Cliquez sur un graphique pour sélectionner les métriques](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Décochez toutes les mesures** pour afficher toutes les options disponibles. Les métriques se répartissent en trois groupes ; lorsqu'un membre d'un groupe est sélectionné, seuls les autres membres de ce groupe s'affichent.
> 
> 

## <a name="metrics"></a>Signification Vignettes de performances et rapports
Vous pouvez accéder à de multiples métriques de performances. Commençons par celles qui s'affichent par défaut dans le volet de l'application.

### <a name="requests"></a>Demandes
Le nombre de demandes HTTP reçues pendant une période spécifiée. Comparez cette valeur avec les résultats des autres rapports pour savoir comment votre application se comporte lorsque la charge à laquelle elle est soumise varie.

Les demandes HTTP incluent toutes les demandes GET ou POST pour les pages, données et images.

Cliquez sur la vignette pour obtenir les décomptes associés aux URL spécifiques.

### <a name="average-response-time"></a>Temps de réponse moyen
Mesure le temps entre le moment où une demande Web est reçue par votre application et le moment où la réponse est renvoyée.

Les points indiquent une moyenne qui évolue. En présence de nombreuses demandes, certaines d'entre elles peuvent s'écarter de la moyenne sans indiquer de pic ou de creux dans le graphique.

Recherchez les pics inhabituels. En règle générale, il est normal que le temps de réponse augmente lorsque le nombre de demandes augmente. Si l'augmentation est disproportionnée, il se peut que votre application ait atteint une limite de ressources telles que l'UC ou la capacité d'un service qu'elle utilise.

Cliquez sur la vignette pour obtenir les temps associés aux URL spécifiques.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Demandes les plus lentes
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Indique les demandes dont les performances doivent probablement être améliorées.

### <a name="failed-requests"></a>Demandes ayant échoué
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Décompte des demandes qui ont généré des exceptions non interceptées.

Cliquez sur la vignette pour afficher le détail des échecs spécifiques, puis sélectionnez une demande individuelle pour afficher ses informations détaillées. 

Seul un échantillon représentatif des échecs est prélevé pour chaque inspection individuelle.

### <a name="other-metrics"></a>Autres métriques
Pour savoir quelles autres métriques vous pouvez afficher, cliquez sur un graphique, puis désélectionnez toutes les métriques afin d'afficher l'ensemble des options disponibles. Cliquez sur (i) pour afficher la définition de chaque métrique.

![Désélectionnez toutes les métriques afin d'afficher l'ensemble des options disponibles](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

La sélection d'une métrique désactive les autres métriques qui peuvent s'afficher sur le même graphique.

## <a name="set-alerts"></a>Définir des alertes
Pour être averti par courrier électronique en cas de valeurs inhabituelles pour une métrique, ajoutez une alerte. Vous pouvez choisir d'envoyer le courrier électronique aux administrateurs de compte ou à des adresses de messagerie spécifiques.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Définissez la ressource avant les autres propriétés. Ne choisissez pas les ressources de test web si vous souhaitez définir des alertes pour les métriques de performances ou d'utilisation.

Veillez à noter les unités dans lesquelles vous êtes invité à entrer la valeur seuil.

*Je ne vois pas apparaître le bouton Ajouter une alerte.*  - S’agit-il d’un compte de groupe auquel vous avez accès en lecture uniquement ? Consultez l’administrateur de ce compte.

## <a name="diagnosis"></a>Problèmes de diagnostic
Voici quelques conseils pour identifier et diagnostiquer les problèmes de performances :

* Configurez des [tests web][availability] pour être alerté en cas de défaillance ou de réponse incorrecte/lente de votre site web. 
* Comparez le nombre de demandes avec les autres métriques afin de savoir si ces défaillances ou réponses lentes sont liées à la charge du site Web.
* [Insérez et recherchez des instructions de trace][diagnostic] dans votre code pour vous aider à identifier les problèmes.

## <a name="next"></a>Étapes suivantes
[Tests web][availability] : faites envoyer des demandes web à votre application à intervalles réguliers et depuis n’importe où dans le monde.

[Capture et recherche de traces de diagnostic][diagnostic] : insérez des appels de trace et passez au crible les résultats afin d’identifier les problèmes.

[Suivi de l’utilisation][usage] : découvrez comment ce que les utilisateurs font avec votre application.

[Résolution des problèmes][qna] et Questions et réponses



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md



