---
title: "Surveillez l'intégrité et l'utilisation de votre application avec Application Insights"
description: Prise en main d'Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications.
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
ms.date: 09/20/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: 9efe10fa35c6a7c84e0d448bbe53127d16d20870
ms.contentlocale: fr-fr
ms.lasthandoff: 09/21/2017

---
# <a name="monitor-performance-in-web-applications"></a>Analyse des performances dans les applications web


Assurez-vous que votre application fonctionne correctement et identifiez rapidement toutes les défaillances éventuelles. [Application Insights][start] vous signale tous les problèmes de performances et les exceptions, et vous aide à trouver et à diagnostiquer les causes principales.

Application Insights peut surveiller les services WCF, ainsi que les applications et services web Java et ASP.NET. Ils peuvent être hébergés localement, sur des machines virtuelles, ou en tant que sites web Microsoft Azure. 

Côté client, Application Insights peut récupérer les données télémétriques des pages web et d’un large éventail d’appareils, notamment des applications iOS, Android et Windows Store.

>[!Note]
> Nous proposons une nouvelle expérience pour identifier les pages qui ralentissent votre application web. Si vous n’y avez pas accès, activez-la en configurant vos options d’aperçu avec le [panneau Aperçu](app-insights-previews.md). Pour en savoir plus sur cette nouvelle expérience, consultez [Rechercher et corriger les goulots d’étranglement avec une analyse de performances interactive](#Find-and-fix-performance-bottlenecks-with-an-interactive-Performance-investigation).

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

## <a name="metrics"></a>Signification Vignettes de performances et rapports
Vous pouvez obtenir plusieurs indicateurs de performance. Commençons par celles qui s'affichent par défaut dans le volet de l'application.

### <a name="requests"></a>Demandes
Le nombre de demandes HTTP reçues pendant une période spécifiée. Comparez cette valeur avec les résultats des autres rapports pour savoir comment votre application se comporte lorsque la charge à laquelle elle est soumise varie.

Les demandes HTTP incluent toutes les demandes GET ou POST de pages, données et images.

Cliquez sur la vignette pour obtenir les décomptes associés aux URL spécifiques.

### <a name="average-response-time"></a>Temps de réponse moyen
Mesure le temps entre le moment où une demande Web est reçue par votre application et le moment où la réponse est renvoyée.

Les points indiquent une moyenne qui évolue. En présence de nombreuses demandes, certaines d'entre elles peuvent s'écarter de la moyenne sans indiquer de pic ou de creux dans le graphique.

Recherchez les pics inhabituels. En règle générale, il est normal que le temps de réponse augmente lorsque le nombre de demandes augmente. Si l'augmentation est disproportionnée, il se peut que votre application ait atteint une limite de ressources telles que l'UC ou la capacité d'un service qu'elle utilise.

Cliquez sur la vignette pour obtenir les temps des URL spécifiées.

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

La sélection d’une métrique désactive celles qui ne peuvent pas s’afficher sur le même graphique.

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
* Surveillez votre application Web en cours avec le [Flux de métriques temps réel][livestream].
* Capturez l’état de votre application .Net avec le [Débogueur de capture instantané][snapshot].

>[!Note]
> Nous sommes en train d’effectuer une transition des analyses de performances d’Application Insights vers un environnement interactif en plein écran. La documentation suivante présente tout d’abord le nouvel environnement, puis examine l’environnement précédent, au cas où vous ayez toujours besoin d’y accéder au cours de la transition, pendant qu’il est encore disponible.

## <a name="find-and-fix-performance-bottlenecks-with-an-interactive-full-screen-performance-investigation"></a>Rechercher et corriger les goulots d’étranglement avec une analyse des performances interactive en plein écran

Vous pouvez utiliser le nouveau mode interactif d’analyse des performances d’Application Insights pour examiner les opérations qui s’exécutent lentement dans votre application web. Vous pouvez sélectionner rapidement une opération lente spécifique et utiliser le [profileur](app-insights-profiler.md) pour analyser les causes profondes du ralentissement jusqu’au niveau du code. À l’aide de la nouvelle distribution de durée indiquée pour l’opération sélectionnée, vous pouvez en un clin d’œil évaluer la gravité du problème pour vos clients. En fait, pour chaque opération lente, vous pouvez voir le nombre des interactions utilisateur qui sont concernées. Dans l’exemple suivant, nous avons décidé d’observer de plus près l’environnement pour l’opération GET Customers/Details. Dans la distribution de la durée, nous constatons que trois pics sont visibles. Celui de gauche indique environ 400 ms et correspond à un environnement présentant une bonne réactivité. Le pic du milieu indique environ 1,2 s et représente un environnement médiocre. Enfin, à 3,6 s, une autre petite pointe représente l’environnement au 99e centile, susceptible de causer l’insatisfaction de nos clients. Cet environnement est dix fois plus lent que l’environnement avancé pour la même opération. 

![Pics de durée de trois GET clients/détails](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Pour obtenir une meilleure idée de l’environnement utilisateur de cette opération, nous pouvons sélectionner une plage temporelle plus longue. Nous pouvons ensuite également réduire l’examen à une fenêtre temporelle spécifique, au cours de laquelle l’opération s’est avérée particulièrement lente. Dans l’exemple suivant, nous sommes passés de la plage par défaut de 24 heures à une plage de 7 jours, puis avons ciblé la fenêtre de temps de 9 h 47 à 12 h 47 entre le mardi 12 et le mercredi 13. Notez que la distribution de durée et le nombre d’exemples et de traces du profileur ont été mis à jour sur la droite.

![Trois pics de durée pour GET Customers/Details sur une plage de 7 jours avec une fenêtre de temps](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Pour mieux cibler les environnements trop lents, nous pouvons ensuite effectuer un zoom sur les durées qui tombent entre le 95e et 99e centile. Celles-ci représentent les 4 % d’interactions utilisateur qui se sont avérées particulièrement lentes.

![Trois pics de durée pour GET Customers/Details sur une plage de 7 jours avec une fenêtre de temps](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Vous pouvez maintenant consulter des exemples représentatifs, en cliquant sur le bouton d’exemples ou sur les traces du profileur représentatives, en cliquant sur le bouton Traces du profileur. Dans cet exemple, quatre traces ont été collectées pour l’opération GET Customers/Details dans la fenêtre de temps et la durée de plage souhaitées.

Il peut arriver que le problème ne se situe pas dans le code, mais dans une dépendance appelée par celui-ci. Vous pouvez basculer vers l’onglet Dépendances dans la vue de triage des performances pour examiner ces dépendances ralenties. Notez que par défaut, la vue des performances présente les moyennes de tendance, mais la valeur à examiner est le 95e centile (ou le 99e lorsque vous surveillez un service très mature). Dans l’exemple suivant, nous avons examiné le ralentissement de la dépendance BLOB Azure, sur laquelle nous appelons PUT fabrikamaccount. Les bonnes expériences se regroupent aux alentours de 40 ms, tandis que les appels lents à la même dépendance sont trois fois plus lents, et se regroupent à 120 ms environ. Il suffit qu’un petit nombre de ces appels s’additionne pour que l’opération en question ralentisse visiblement. Vous pouvez explorer les exemples représentatifs et les traces du profileur de la même manière que dans l’onglet Opérations.

![Trois pics de durée pour GET Customers/Details sur une plage de 7 jours avec une fenêtre de temps](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

L’intégration avec Insights est une autre fonctionnalité très puissante de la nouvelle analyse des performances en plein écran. Application Insights peut détecter et faire apparaître sous forme de régressions de réactivité les propriétés communes dans l’exemple de jeu que vous avez décidé d’examiner, ainsi que vous aider à les identifier. La meilleure façon d’examiner toutes les analyses disponibles consiste à passer sur une période de 30 jours, puis à sélectionner Global pour avoir une visibilité sur toutes les opérations du mois passé.

![Trois pics de durée pour GET Customers/Details sur une plage de 7 jours avec une fenêtre de temps](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)

Application Insights dans la nouvelle vue de triage des performances vous aide littéralement à retrouver « l’aiguille dans la botte de foin » qui est à l’origine de la dégradation de performances pour les utilisateurs de votre application web.

## <a name="deprecated-find-and-fix-performance-bottlenecks-with-a-narrow-bladed-legacy-performance-investigation"></a>Déconseillé : rechercher et corriger les goulots d’étranglement des performances avec une investigation des performances héritée à panneau étroit

Vous pouvez utiliser l’analyse de performances à panneaux héritée d’Application Insights pour localiser les parties de votre application Web qui ralentissent les performances globales. Vous pouvez trouver des pages spécifiques qui sont ralenties et utiliser le [profileur](app-insights-profiler.md) pour effectuer un suivi des causes profondes de ces problèmes, jusqu’au niveau du code. 

### <a name="create-a-list-of-slow-performing-pages"></a>Créer la liste des pages lentes 

La première étape pour détecter les problèmes de performances consiste à obtenir la liste des pages qui répondent lentement. La capture d’écran ci-dessous montre comment utiliser le panneau de performances pour répertorier les pages à analyser plus en détail. Cette page montre que le temps de réponse de l’application ralentit à environ 6h00 puis à 22h00. Vous pouvez également voir que l’opération GET sur le client/les détails contenait des opérations avec un temps de réponse médian de 507,05 millisecondes. 

![Performances interactives d’Application Insights](./media/app-insights-web-monitor-performance/performance1.png)

### <a name="drill-down-on-specific-pages"></a>Examen approfondi de pages

Dès que vous avez un instantané des performances de votre application, vous pouvez obtenir plus d’informations sur les certaines opérations lentes. Cliquez sur une opération dans la liste pour afficher les détails, comme indiqué ci-dessous. Dans ce graphique, vous pouvez voir si les performances étaient basées sur une dépendance. Vous pouvez également voir combien d’utilisateurs ont été confrontés aux différents temps de réponse. 

![Panneau des opérations d’Application Insights](./media/app-insights-web-monitor-performance/performance5.png)

### <a name="drill-down-on-a-specific-time-period"></a>Examen approfondi d’une période

Une fois que vous avez identifié un point dans le temps à analyser, examinez-le plus en détail pour identifier les opérations à l’origine du ralentissement des performances. Lorsque vous cliquez sur un point dans le temps, vous obtenez les détails de la page, comme indiqué ci-dessous. Dans l’exemple ci-dessous, vous voyez les opérations d’une période donnée, avec les codes de réponse du serveur et la durée de l’opération. Vous voyez également l’URL qui permet d’ouvrir un élément de travail TFS si vous avez besoin d’envoyer ces informations à votre équipe de développement.

![Tranche horaire d’Application Insights](./media/app-insights-web-monitor-performance/performance2.png)

### <a name="drill-down-on-a-specific-operation"></a>Examen approfondi d’une opération

Une fois que vous avez identifié un point dans le temps à analyser, examinez-le plus en détail pour identifier les opérations à l’origine du ralentissement des performances. Cliquez sur une opération dans la liste pour en afficher les détails, comme indiqué ci-dessous. Dans cet exemple, vous constatez que l’opération a échoué et qu’Application Insights a fourni les détails de l’exception levée par l’application. Là encore, vous pouvez facilement créer un élément de travail TFS à partir de ce panneau.

![Panneau des opérations d’Application Insights](./media/app-insights-web-monitor-performance/performance3.png)


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
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md




