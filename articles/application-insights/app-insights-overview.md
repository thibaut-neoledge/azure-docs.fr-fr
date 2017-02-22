---
title: Gestion des performances des applications avec Azure Application Insights | Microsoft Docs
description: "Suivez les performances et l’utilisation de votre application web en ligne.  Détection, tri et diagnostic des problèmes"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 3e9476f8edc4186af026495bc575b8203c35c619
ms.openlocfilehash: b01de7724ec116e5e27369f7c0f852f82ab0348b


---
# <a name="application-performance-management-with-application-insights"></a>Gestion des performances des applications avec Application Insights
Application Insights est un service de gestion des performances des applications (APM) extensible destiné aux développeurs web. Utilisez-le pour analyser votre application web en direct. Ce service détecte automatiquement les problèmes de performances. Il intègre de puissants outils d’analyse pour vous aider à diagnostiquer les problèmes et à comprendre ce que font les utilisateurs avec votre application.  Il a été conçu pour vous permettre d’améliorer continuellement les performances et la convivialité. Il fonctionne avec des applications sur un large éventail de plates-formes, notamment .NET, Node.js et J2EE, hébergées sur site ou dans le cloud. En outre, ce service s’intègre à votre processus devOps et offre des points de connexion vers un large éventail d’autres outils.

![Créez un graphique des statistiques d'activité de l’utilisateur ou explorez des événements spécifiques.](./media/app-insights-overview/00-sample.png)

[Consultez l’animation de présentation](https://www.youtube.com/watch?v=fX2NtGrh-Y0).

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?
Vous installez un petit package d’instrumentation dans votre application puis configurez une ressource Application Insights dans le portail Microsoft Azure. L’instrumentation surveille votre application et envoie les données de télémétrie au portail. Le portail vous montre des graphiques statistiques et fournit de puissants outils de recherche pour vous aider à diagnostiquer les problèmes.

![L’instrumentation Application Insights dans votre application envoie la télémétrie aux ressources Application Insights dans le portail Azure.](./media/app-insights-overview/01-scheme.png)

Application Insights dispose de plusieurs [modules d’instrumentation standard](app-insights-configuration-with-applicationinsights-config.md) qui collectent différents types de données de télémétrie, comme le temps de réponse aux demandes, les exceptions et les appels de dépendance. Vous pouvez également [écrire du code pour envoyer des données de télémétrie personnalisées](app-insights-api-custom-events-metrics.md) sur le portail.

### <a name="whats-the-overhead"></a>Quelle est la surcharge ?
L'impact sur les performances de votre application est très faible. Le suivi des appels n’entraîne aucun blocage, et les appels sont regroupés par lots et envoyés dans un thread séparé.

## <a name="what-does-it-do"></a>Fonction
Application Insights est destiné à l’équipe de développement et permet de comprendre les performances et l’utilisation de votre application. Elle fournit :

des types de télémétrie :

* Taux de demandes HTTP, temps de réponse, taux de réussite
* Taux d’appels de dépendance (HTTP et SQL), temps de réponse, taux de réussite
* Traces des exceptions du serveur et du client
* Suivi du journal de diagnostics
* Nombre de pages consultées, nombre d’utilisateurs et de sessions, temps de chargement du navigateur, exceptions
* Taux d’appels AJAX, temps de réponse et taux de réussite
* Compteurs de performances du serveur
* Données de télémétrie personnalisées du client et du serveur
* Segmentation par emplacement client, version du navigateur, version du système d’exploitation, instance de serveur, dimensions personnalisées, et bien plus encore
* Tests de disponibilité

des outils de diagnostic et d’analyse :

* Alertes intelligentes et manuelles sur les taux d’échec, d’autres mesures et la disponibilité
* Graphiques des métriques agrégées au fil du temps
* Recherche de diagnostic sur les instances de demandes, les exceptions, les événements personnalisés, le suivi du journal, les affichages de page, les appels de dépendance et AJAX
* Analytics - un puissant langage de requête sur vos données de télémétrie
* Tableaux de bord - composez les graphiques dont vous avez besoin pour surveiller tous vos composants d’application

## <a name="how-do-i-use-it"></a>Comment l’utiliser ?
### <a name="monitor"></a>Surveiller
Installez Application Insights dans votre application web, configurez les tests web de disponibilité, puis :

* Configurez un tableau de bord pour la salle de réunion afin de garder un œil sur la charge, la réactivité et la performance de vos dépendances, chargements de page et appels AJAX.
* Découvrez quelles demandes sont les plus lentes et se terminent le plus par un échec.
* Regardez le flux en direct lorsque vous déployez une nouvelle version, afin de savoir immédiatement s’il y a d’éventuelles dégradations.

### <a name="diagnose"></a>Diagnostic
Lorsque vous recevez une alerte ou découvrez un problème :

* Mettez en corrélation les échecs et les exceptions, les appels de dépendance et les traces.
* Examinez les journaux de suivi et les vidages de pile.

### <a name="assess"></a>Évaluation
Mesurez l’efficacité de chaque nouvelle fonctionnalité que vous déployez.

* Planifiez l’évaluation de la manière dont les clients utilisent de nouvelles fonctionnalités d’entreprise ou utilisateur.
* Écrivez des données de télémétrie personnalisées dans votre code pour enregistrer l’utilisation.
* Basez chaque cycle de développement sur les preuves tangibles des données de télémétrie.

## <a name="get-started"></a>Prise en main
Application Insights est un des nombreux services hébergés dans Microsoft Azure, et les données de télémétrie sont envoyées à des fins d’analyse et de présentation. Ainsi, avant d’effectuer une autre opération, vous devez souscrire un abonnement [Microsoft Azure](http://azure.com). L’inscription est gratuite ; de plus, si vous choisissez le [plan de tarification](https://azure.microsoft.com/pricing/details/application-insights/) de base d’Application Insights, rien n’est facturé tant que votre application n’est pas utilisée de façon substantielle. Si votre organisation possède déjà un abonnement, votre compte Microsoft peut y être ajouté.

Il existe plusieurs façons de démarrer avec Application Insights. Commencez par la méthode qui vous convient le mieux. Vous pourrez ajouter les autres par la suite.

* **Au moment de l’exécution : instrumentez votre application web sur le serveur.** Permet d’éviter toute mise à jour du code. Vous avez besoin de l’accès administrateur à votre serveur.
  * [**IIS local ou sur une machine virtuelle**](app-insights-monitor-performance-live-website-now.md)
  * [**Application web ou machine virtuelle Azure**](app-insights-monitor-performance-live-website-now.md)
  * [**J2EE**](app-insights-java-live.md)
* **Au moment du développement : ajoutez Application Insights à votre code.** Vous permet d’écrire des données de télémétrie personnalisées et d’instrumenter le serveur principal et les applications de bureau.
  * [Visual Studio](app-insights-asp-net.md) 2013 Update 2 ou version ultérieure.
  * Java dans [Eclipse](app-insights-java-eclipse.md) ou [autres outils](app-insights-java-get-started.md)
  * [Node.JS](app-insights-nodejs.md)
  * [autres plateformes](app-insights-platforms.md)
* **[Instrumentez vos pages web](app-insights-javascript.md)** pour l’affichage de page, AJAX et d’autres données de télémétrie côté client.
* **[Tests de disponibilité](app-insights-monitor-web-app-availability.md)** : effectuez régulièrement un test ping sur votre site web à partir de nos serveurs.

> [!NOTE]
> À ce stade, vous souhaitez peut-être simplement vous lancer et expérimenter. Mais, si vous souhaitez voir ce dont Application Insights est capable, lisez la suite...
>
>

## <a name="explore-metrics"></a>Exploration des mesures
Exécutez votre application, que ce soit en mode débogage sur votre ordinateur de développement, ou en la déployant sur un serveur, et utilisez-la pendant quelques temps. Puis, connectez-vous au [portail Azure](https://portal.azure.com).

Accédez au panneau de vue d’ensemble de votre application Application Insights :

![Le panneau Vue d’ensemble](./media/app-insights-overview/01-find.png)

La vue d’ensemble vous permet de voir immédiatement comment votre application s’exécute. Vous pouvez comparer la charge (en termes de taux de demandes) et le temps nécessaire à votre application pour répondre aux requêtes. En cas de hausse disproportionnée du temps de réponse lorsque la charge augmente, vous pouvez allouer davantage de ressources à votre application. Si plusieurs échecs de réponse se produisent immédiatement après le déploiement d’une nouvelle build, vous pouvez effectuer une restauration.

#### <a name="get-more-detail"></a>Obtenir plus de détails
Cliquez sur un graphique pour obtenir un ensemble de mesures plus détaillé. Par exemple, le graphique des temps de réponse du serveur conduit vers d’autres graphiques montrant les taux de demandes, les temps de réponse et les temps de réponse de dépendances (autrement dit, les services qui appellent votre application).  

![Panneau Serveurs](./media/app-insights-overview/04.png)

Le graphique de dépendances est utile car il vous permet de déterminer si les bases de données et les API REST que votre application utilise répondent également ou entraînent des retards.

#### <a name="customize-a-chart"></a>Personnaliser un graphique
Essayez de modifier un de ces graphiques. Par exemple, si votre application web s’exécute sur une collection d’instances de serveur, vous pouvez comparer les temps de réponse sur les différentes instances de serveur :

![modifier le graphique](./media/app-insights-overview/05.png)

1. Placez le pointeur sur le graphique puis cliquez sur Modifier.
2. Choisissez une mesure. Plusieurs mesures peuvent être affichées sur un graphique, mais uniquement dans certaines combinaisons : vous devrez peut-être désélectionner une mesure avant de sélectionner celle que vous souhaitez.
3. Utilisez l’option Groupé pour segmenter une mesure selon une propriété. Dans cet exemple, nous affichons des lignes distinctes pour les temps de réponse.

    Notez que vous devez sélectionner une propriété valide pour la mesure ; sinon, le graphique n’affiche pas toutes les données.
4. Sélectionnez un type de graphique. Les graphiques à aires et à barres offrent un affichage empilé adapté au type d’agrégation « Somme ».

[En savoir plus sur l’exploration des mesures](app-insights-metrics-explorer.md).

## <a name="search-instance-data"></a>Recherche de données d’instance
Pour étudier un problème, il est utile d’inspecter des instances d’événements spécifiques.

Cliquez sur un graphique de mesures pour parcourir les données d’instance avec les filtres et l’intervalle de temps appropriés. Par exemple, faites défiler le nombre de requêtes de serveur pour afficher des rapports de requête individuels.

Vous pouvez également accéder directement aux données d’instance à partir de la recherche dans la page Vue d’ensemble :

![Recherche d’instances](./media/app-insights-overview/06.png)

Utilisez des filtres pour vous concentrer sur certains types d’événements et des valeurs de propriétés choisies :

![Filtrer des propriétés](./media/app-insights-overview/07.png)

Cliquez sur « ... » pour afficher une liste complète des propriétés, ou ouvrez d’autres événements associés à la même demande. Dans cet exemple, la requête ayant échoué comporte un état d’exception associé :

![Articles connexes et détails des propriétés](./media/app-insights-overview/08.png)

Ouvrez un événement - dans cet exemple, l’exception associée - et vous pouvez créer un élément de travail (si vous utilisez Visual Studio Team Services pour effectuer le suivi des tâches).

![Création d’un élément de travail](./media/app-insights-overview/09.png)

## <a name="analytics"></a>Analyse
[Analytics](app-insights-analytics.md) est une fonctionnalité de recherche et d’analyse encore plus puissante, dans laquelle vous pouvez écrire des requêtes de type SQL sur vos données de télémétrie, pour rechercher des problèmes spécifiques ou pour compiler des informations statistiques.

![Analyse](./media/app-insights-overview/10.png)

Ouvrez la fenêtre du didacticiel pour afficher et exécuter des exemples de requêtes sur vos données, ou suivez plus en détail la [procédure du didacticiel](app-insights-analytics-tour.md). IntelliSense vous présente les requêtes que vous pouvez utiliser et vous fournit [une référence complète du langage](app-insights-analytics-reference.md).

Les requêtes commencent généralement par le nom d’un flux de données de télémétrie telles que des demandes, des exceptions ou des dépendances. Ouvrez la barre de schéma à gauche pour afficher la liste des flux de données de télémétrie disponibles. La requête est un pipeline [d’opérations de requête](app-insights-analytics-reference.md#queries-and-operators) de type `where` - un filtre booléen - ou `project` -qui calcule les nouvelles propriétés. `summarize` agrège les instances, les regroupe selon des fonctions que vous définissez, puis applique des fonctions d’agrégation aux données groupées.

Les résultats peuvent être [affichés dans des tables ou divers types de graphique](app-insights-analytics-tour.md#charting-the-results).

## <a name="custom-telemetry"></a>Télémétrie personnalisée
La télémétrie intégrée que vous obtenez en installant simplement Application Insights vous permet d’analyser les nombres, les taux de réussite et les temps de réponse à la fois pour les demandes web vers votre application et les dépendances : autrement dit, les appels depuis votre application SQL et les API REST. Vous obtenez également des traces des exceptions et (avec Status Monitor sur votre serveur) des compteurs de performances système. Si vous ajoutez l’extrait de code client à vos pages web, vous obtenez le nombre de pages consultées, les temps de chargement, les exceptions client et les taux de réussite et de réponse des appels AJAX.

L’analyse de toutes ces données de télémétrie peut vous en apprendre beaucoup sur les performances et l’utilisation de votre application. Mais parfois cela n’est pas suffisant. Vous souhaiterez peut-être surveiller la longueur d’une file d’attente afin d’ajuster les performances ; ou compter les ventes et les segmenter par site ; ou, côté client, découvrir comment les utilisateurs cliquent sur un bouton afin d’optimiser l’expérience utilisateur.

[L’API Application Insights](app-insights-api-custom-events-metrics.md) fournit les appels `TrackEvent(name)` et `TrackMetric(name, value)` pour vous permettre d’envoyer vos propres événements et mesures personnalisés. Il existe des appels équivalents côté client.

Par exemple, si votre page web est une application de jeu à une seule page, vous pouvez insérer une ligne dans les emplacements appropriés pour consigner les victoires ou les défaites de l’utilisateur :

    appInsights.trackEvent("WinGame");
    ...
    appInsights.trackEvent("LoseGame");

Nous pouvons ensuite créer un graphique comptabilisant les événements personnalisés, en les segmentant par nom d’événement :

![Analyse](./media/app-insights-overview/11.png)

### <a name="log-traces"></a>Suivis de journaux
À des fins de diagnostic, il existe un événement personnalisé `TrackTrace(message)` que vous pouvez utiliser pour les traces d’exécution. Dans les fonctionnalités de recherche et d’analyse, vous pouvez rechercher dans le contenu du message, ce qui peut être plus long que pour un nom d’événement.

Si vous utilisez déjà un framework de journalisation comme Log4Net, NLog, Log4J ou System.Diagnostic.Trace, ces appels de trace peuvent être capturés par Application Insights et s’afficheront à côté des autres données de télémétrie. Les outils Visual Studio ajoutent automatiquement le module SDK approprié.

## <a name="profiling-your-live-site"></a>Profilage de votre site dynamique

Vous voulez savoir à quoi tout ce temps a été consacré ? Le profileur d’Application Insights effectue le suivi des appels HTTP vers votre site dynamique et vous indique les fonctions de votre code qui ont pris le plus de temps. Le profileur est actuellement en version préliminaire limitée : vous pouvez [vous inscrire pour l’essayer](https://aka.ms/AIProfilerPreview).

## <a name="dashboards"></a>Tableaux de bord
De nombreuses applications sont constituées de plusieurs composants, comme un service web et un ou plusieurs processeurs principaux. Chaque composant sera surveillé par une ressource Application Insights distincte. Si votre système s’exécute sur Azure, vous pouvez utiliser - et surveiller - des services tels que des concentrateurs d’événements et l’apprentissage automatique (Machine Learning).

Pour surveiller l’intégralité de votre système, vous pouvez sélectionner les graphiques les plus intéressants à partir de différentes applications et les épingler sur le [tableau de bord](app-insights-dashboards.md)Azure afin de garder constamment un œil sur l’ensemble du système.

![Tableaux de bord](./media/app-insights-overview/12.png)

En fait, vous pouvez créer plusieurs tableaux de bord : par exemple, un tableau de bord d’équipe pour surveiller l’état général du système ; un tableau de bord de conception qui se concentre sur l’utilisation des différentes fonctionnalités ; un tableau de bord distinct pour les composants testés, et ainsi de suite.  

Comme les ressources, les tableaux de bord peuvent être partagés entre les membres de l’équipe.

## <a name="development-in-visual-studio"></a>Développement dans Visual Studio
Si vous utilisez Visual Studio pour développer votre application, vous y trouverez plusieurs outils Application Insights intégrés.

### <a name="diagnostic-search"></a>Recherche de diagnostic
La fenêtre de recherche présente les événements qui ont été consignés. (Si vous étiez connecté à Azure au moment de l’installation d’Application Insights, vous pouvez rechercher ces mêmes événements dans le portail.)

![Cliquez avec le bouton droit sur le projet et sélectionnez Application Insights, Rechercher.](./media/app-insights-overview/34.png)

La recherche en texte libre fonctionne sur tous les champs des événements. Par exemple, vous pouvez effectuer une recherche sur une partie de l’URL d’une page, sur la valeur d’une propriété telle que la ville du client, ou encore sur des termes spécifiques contenus dans un journal de suivi.

Cliquez sur n’importe quel événement pour afficher le détail de ses propriétés.

Vous pouvez également ouvrir l’onglet Éléments connexes pour aider à repérer les demandes ayant échoué ou les exceptions.

![](./media/app-insights-overview/41.png)

### <a name="diagnostics-hub"></a>Concentrateur de diagnostic
Le concentrateur de diagnostic (Visual Studio 2015 ou version ultérieure) affiche les données de télémétrie du serveur Application Insights au moment où elles sont générées. Il fonctionne même si vous avez choisi d’installer le SDK sans le connecter à une ressource dans le portail Azure.

![Ouvrez la fenêtre Outils de diagnostic et passez en revue les événements Application Insights.](./media/app-insights-overview/31.png)

### <a name="exceptions"></a>Exceptions
Si vous avez [configuré l’analyse des exceptions](app-insights-asp-net-exceptions.md), les rapports d’exception s’afficheront dans la fenêtre de recherche.

Cliquez sur une exception pour obtenir une trace de pile. Si le code de l’application est ouvert dans Visual Studio, vous pouvez utiliser la trace de pile pour accéder à la ligne de code recherchée.

![Arborescence des appels de procédure d’exception](./media/app-insights-overview/17.png)

En outre, le nombre d’exceptions enregistrées par Application Insights dans les dernières 24 heures est indiqué dans la ligne de filtre Code au-dessus de chaque méthode.

![Arborescence des appels de procédure d’exception](./media/app-insights-overview/21.png)

### <a name="local-monitoring"></a>Surveillance locale
(À partir de Visual Studio 2015 Mise à jour 2) Si vous n’avez pas configuré le Kit de développement logiciel pour envoyer les données de télémétrie au portail Application Insights (et qu’il n’existe donc aucune clé d’instrumentation dans ApplicationInsights.config), la fenêtre de diagnostic affichera les données de télémétrie de votre dernière session de débogage.

C’est le comportement adéquat si vous avez déjà publié une version antérieure de votre application. Vous ne voulez pas que les données de télémétrie de vos sessions de débogage soient confondues avec les données de télémétrie sur le portail Application Insights de l’application publiée.

Cela est également utile si vous disposez de [données de télémétrie personnalisées](app-insights-api-custom-events-metrics.md) que vous souhaitez déboguer avant de les envoyer sur le portail.

* *Dans un premier temps, j’ai entièrement configuré Application Insights pour envoyer des données de télémétrie au portail. À présent, j’aimerais afficher ces données uniquement dans Visual Studio.*

  * Dans les paramètres de la fenêtre de recherche, il existe une option permettant de rechercher des diagnostics locaux même si votre application envoie des données de télémétrie au portail.
  * Pour arrêter l’envoi de données de télémétrie au portail, commentez la ligne `<instrumentationkey>...` du fichier ApplicationInsights.config. Lorsque vous êtes prêt à envoyer de nouveau les données de télémétrie au portail, supprimez les commentaires.

### <a name="trends"></a>Trends
Trends est un outil de Visual Studio qui permet de visualiser le comportement de votre application au fil du temps.

Sélectionnez **Explorer les tendances de la télémétrie** à partir du bouton de la barre d’outils Application Insights ou de la fenêtre de recherche d’Application Insights. Sélectionnez l’une des cinq requêtes courantes pour commencer. Vous pouvez analyser différents jeux de données en fonction des types de télémétrie, des intervalles de temps ainsi que d’autres propriétés.

Pour rechercher des anomalies dans vos données, sélectionnez l’une des options d’anomalie dans la liste déroulante « Type de vue ». Les options de filtrage en bas de la fenêtre facilitent l’obtention de sous-ensembles spécifiques de votre télémétrie.

![Trends](./media/app-insights-overview/51.png)

## <a name="releasing-a-new-build"></a>Publication d’une nouvelle build
### <a name="live-metrics-stream"></a>Live Metrics Stream (Flux continu de mesures)
Live Metrics Stream indique les mesures de votre application à ce moment précis avec une latence en temps réel proche d’1 seconde. Cette fonctionnalité est très utile lorsque vous libérez une nouvelle build et souhaitez vous assurer que tout fonctionne comme prévu, ou examinez d’un incident en temps réel.

![Dans le panneau Vue d'ensemble, cliquez sur Live Stream](./media/app-insights-overview/live-stream.png)

Contrairement à Metrics Explorer, Live Metrics Stream affiche un ensemble fixe de mesures. Les données persistent uniquement tant qu’elles se trouvent sur le graphique et sont ensuite ignorées.

### <a name="annotations"></a>Annotations
[annotations de version](app-insights-annotations.md) sur les graphiques de mesures montrent les endroits où vous avez déployé une nouvelle édition. Elles vous permettent de mieux voir l’effet de vos modifications sur les performances de votre application. Elles peuvent être créées automatiquement par le [système de génération Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs) ou par des déploiements web à partir de Visual Studio. Vous pouvez également [les créer à partir de PowerShell](#create-annotations-from-powershell).

![Exemples d’annotations avec corrélation visible avec le délai de réponse de serveur](./media/app-insights-overview/00.png)

Les annotations de version sont une fonctionnalité de la build sur le cloud et le service de version de Visual Studio Team Services.



## <a name="alerts"></a>Alertes
En cas de problème avec votre application, vous voudrez immédiatement en connaître l’origine.

Application Insights offre trois types d’alertes remises par courrier électronique.

### <a name="proactive-diagnostics"></a>Diagnostics proactifs
Les [diagnostics proactifs](app-insights-proactive-failure-diagnostics.md) sont automatiquement configurés : aucune action requise de votre part. Si votre site a suffisamment de trafic, vous recevrez un message électronique en cas de hausse des demandes ayant échoué à un moment inhabituel de la journée ou pour un certain taux de demandes. L’alerte contient des informations de diagnostic.

Voici un exemple d’alerte.

![Exemple d’alerte intelligente affichant l’analyse du cluster au moment de l’échec](./media/app-insights-overview/proactive-alert.png)

Un second type de détection proactive détermine les corrélations entre les défaillances et des facteurs tels que l’emplacement, le système d’exploitation client ou le type de navigateur.

### <a name="metric-alerts"></a>Alertes de métrique
Vous pouvez configurer les [alertes de métrique](app-insights-alerts.md) pour vous indiquer quand une métrique dépasse une valeur seuil pendant une certaine période, comme le nombre d’échecs, la mémoire ou les affichages de page.

![Dans Metrics Explorer, sélectionnez Règles d'alerte, Ajouter une alerte](./media/app-insights-overview/appinsights-413setMetricAlert.png)

### <a name="availability"></a>Availability
[tests de disponibilité web](app-insights-monitor-web-app-availability.md) envoient des demandes à votre site à partir de nos serveurs depuis différents emplacements dans le monde. Ils vous indiquent quand votre site est indisponible sur Internet ou répond lentement.

![Exemple de test web](./media/app-insights-overview/appinsights-10webtestresult.png)

## <a name="export-and-api"></a>Exportation et API
Il existe plusieurs façons d’exporter vos données de télémétrie à partir du portail Application Insights :

* [L’API REST d’accès aux données](https://dev.applicationinsights.io/) peut être utilisée pour rechercher et extraire des données, notamment l’exécution de requêtes Analytics. 
* Exportez des [requêtes Analytics vers des tableaux de bord Power BI](app-insights-export-power-bi.md) et affichez les résultats dans les visualisations Power BI, qui peuvent être automatiquement actualisées.
* [exportation continue](app-insights-export-telemetry.md) est idéale si vous souhaitez conserver la majeure partie de vos données de télémétrie plus longtemps que la période de rétention standard.
* Les tables de [mesures](app-insights-metrics-explorer.md#export-to-excel), les résultats de recherche et les résultats [d’analyse](app-insights-analytics.md) peuvent être exportés vers une feuille de calcul Excel.

![Affichage des données dans Power BI](./media/app-insights-overview/210.png)

## <a name="data-management"></a>Gestion des données
Il existe des limites à l’utilisation d’Application Insights, qui dépendent dans une certaine mesure du mécanisme de tarification que vous choisissez. Les principales limites sont :

* Le taux de télémétrie par minute
* Le nombre de points de données par mois
* La période de rétention des données

[échantillonnage](app-insights-sampling.md) est un mécanisme permettant de réduire le coût et d’éviter la limitation. Il rejette une proportion de vos données de télémétrie pour n’en conserver qu’un échantillon représentatif. Les éléments associés (par exemple les exceptions et les requêtes qui les ont causés) sont conservés ou supprimés ensemble. Pour les applications ASP.NET, l’échantillonnage est automatique et exécuté dans l’application ; sinon, vous pouvez choisir de l’appliquer à l’ingestion vers le portail.

## <a name="next-steps"></a>Étapes suivantes
Prise en main lors de l’exécution avec :

* [Serveur IIS](app-insights-monitor-performance-live-website-now.md)
* [Serveur J2EE](app-insights-java-live.md)

Prise en main pendant le développement avec :

* [ASP.NET](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Node.JS](app-insights-nodejs.md)

## <a name="support-and-feedback"></a>Support et commentaires
* Questions et problèmes :
  * [Résolution des problèmes][qna]
  * [Forum MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [Stackoverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
  * [Obtenir un support Developer Support](app-insights-get-dev-support.md)
* Vos suggestions :
  * [UserVoice](https://visualstudio.uservoice.com/forums/357324)
* Blog :
  * [Blog Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

## <a name="videos"></a>Vidéos
[![Présentation animée](./media/app-insights-overview/video-front-1.png)](https://www.youtube.com/watch?v=fX2NtGrh-Y0)

> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/218/player]
>
> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/231/player]
>
> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/222/player]
>
> [Animation de présentation](https://www.youtube.com/watch?v=fX2NtGrh-Y0)
>
>

<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md



<!--HONumber=Jan17_HO4-->


