<properties 
	pageTitle="Analyse d’une application web pour ASP.NET avec Application Insights" 
	description="Performances, disponibilité et analyse de l’utilisation de votre site web ASP.NET, hébergé en local ou dans Azure." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/12/2016" 
	ms.author="awills"/>


# Configurer Application Insights pour ASP.NET


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Le kit de développement logiciel (SDK) Application Insights envoie les données de télémétrie d’analyse de votre application web en ligne au portail Azure. Vous pouvez vous connecter à partir de celui-ci et afficher des graphiques concernant les performances et l’utilisation de votre application.

![Exemples de graphiques d’analyse des performances](./media/app-insights-asp-net/10-perf.png)

Vous pouvez rechercher et mettre en corrélation des demandes, des exceptions et des événements de journal spécifiques. Vous pouvez utiliser l’API pour ajouter des données de télémétrie pour surveiller les performances et l’utilisation dans les détails.

#### Avant de commencer

Ce dont vous avez besoin :

* Un abonnement à [Microsoft Azure](http://azure.com). Si votre équipe ou votre organisation dispose d’un abonnement Azure, le propriétaire peut vous y ajouter à l’aide de votre [compte Microsoft](http://live.com).
* Visual Studio 2013 Update 3 ou version ultérieure.

## <a name="ide"></a> Ajouter Application Insights à votre projet dans Visual Studio

#### S'il s'agit d'un nouveau projet...

Lorsque vous créez un projet dans Visual Studio, assurez-vous que Application Insights est sélectionné.


![Création d'un projet ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

Sélectionnez un compte avec une authentification Azure. Vous devrez peut-être saisir de nouveau vos informations d’identification (si vous ne vous connectez pas, le code du SDK sera ajouté et vous pourrez le configurer ultérieurement).


#### ... ou s'il s'agit d'un projet existant

Cliquez avec le bouton droit dans l’Explorateur de solutions et sélectionnez **Ajouter Application Insights** ou **Configurer Application Insights**.

![Sélection de Ajouter Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)


#### Options d'installation

S’il s’agit de votre toute première utilisation, vous serez invité à vous connecter ou à vous inscrire à Microsoft Azure.

Si cette application fait partie d'une application plus importante, vous pouvez utiliser **Configurer les paramètres** pour la placer dans le même groupe de ressources que les autres composants.


####<a name="land"></a> Quelle est la fonction de la commande « Ajouter Application Insights » ?

Cette commande effectue les opérations suivantes (que vous pouvez [exécuter manuellement](app-insights-start-monitoring-app-health-usage.md) si vous préférez) :

1. Elle ajoute le package NuGet du Kit de développement logiciel (SDK) Web Application Insights à votre projet. Pour le visualiser dans Visual Studio, cliquez avec le bouton droit sur votre projet et choisissez Gérer les packages NuGet.
2. Elle crée une ressource Application Insights dans [le portail Azure][portal]. Il s’agit de l’endroit où s’afficheront vos données. Elle récupère la *clé d’instrumentation*, qui identifie la ressource.
3. Insère la clé d’instrumentation dans `ApplicationInsights.config` pour permettre au SDK d’envoyer les données de télémétrie au portail.

Si vous ne vous connectez pas d’abord à Azure, le SDK sera installé sans être connecté à une ressource. Vous pourrez voir et rechercher les données de télémétrie Application Insights dans la fenêtre de recherche de Visual Studio au cours d’une opération de débogage. Vous pourrez effectuer les autres étapes ultérieurement.

## <a name="run"></a> Débogage de votre projet

Exécutez votre application à l'aide de la touche F5 et essayez-la : ouvrez différentes pages pour générer des données de télémétrie.

Un décompte des événements consignés s’affiche dans Visual Studio.

![Dans Visual Studio, le bouton Application Insights apparaît pendant le débogage.](./media/app-insights-asp-net/appinsights-09eventcount.png)

Cliquez sur ce bouton pour ouvrir la recherche de diagnostic.



## Débogage de la télémétrie

### Concentrateur de diagnostic

Le concentrateur de diagnostic (Visual Studio 2015 ou version ultérieure) affiche les données de télémétrie du serveur Application Insights au moment où elles sont générées. Il fonctionne même si vous avez choisi d’installer le SDK sans le connecter à une ressource dans le portail Azure.

![Ouvrez la fenêtre Outils de diagnostic et passez en revue les événements Application Insights.](./media/app-insights-asp-net/31.png)


### Recherche de diagnostic

La fenêtre de recherche présente les événements qui ont été consignés. (Si vous étiez connecté à Azure au moment de l’installation d’Application Insights, vous pouvez rechercher ces mêmes événements dans le portail.)

![Cliquez avec le bouton droit sur le projet et sélectionnez Application Insights, Rechercher.](./media/app-insights-asp-net/34.png)

La recherche en texte libre fonctionne sur tous les champs des événements. Par exemple, vous pouvez effectuer une recherche sur une partie de l’URL d’une page, sur la valeur d’une propriété telle que la ville du client, ou encore sur des termes spécifiques contenus dans un journal de suivi.

Vous pouvez également ouvrir l’onglet Éléments connexes pour aider à repérer les demandes ayant échoué ou les exceptions.


![](./media/app-insights-asp-net/41.png)



### Exceptions

Si vous avez [configuré l’analyse des exceptions](app-insights-asp-net-exceptions.md), les rapports d’exception s’afficheront dans la fenêtre de recherche.

Cliquez sur une exception pour obtenir une trace de pile. Si le code de l’application est ouvert dans Visual Studio, vous pouvez utiliser la trace de pile pour accéder à la ligne de code recherchée.




### Surveillance locale



(À partir de Visual Studio 2015 Mise à jour 2) Si vous n’avez pas configuré le Kit de développement logiciel pour envoyer les données de télémétrie au portail Application Insights (et qu’il n’existe donc aucune clé d’instrumentation dans ApplicationInsights.config), la fenêtre de diagnostic affichera les données de télémétrie de votre dernière session de débogage.

C’est le comportement adéquat si vous avez déjà publié une version antérieure de votre application. Vous ne voulez pas que les données de télémétrie de vos sessions de débogage soient confondues avec les données de télémétrie sur le portail Application Insights de l’application publiée.

Cela est également utile si vous disposez de [données de télémétrie personnalisées](app-insights-api-custom-events-metrics.md) que vous souhaitez déboguer avant de les envoyer sur le portail.


* *Dans un premier temps, j’ai entièrement configuré Application Insights pour envoyer des données de télémétrie au portail. À présent, j’aimerais afficher ces données uniquement dans Visual Studio.*

 * Dans les paramètres de la fenêtre de recherche, il existe une option permettant de rechercher des diagnostics locaux même si votre application envoie des données de télémétrie au portail.
 * Pour arrêter l’envoi de données de télémétrie au portail, commentez la ligne `<instrumentationkey>...` du fichier ApplicationInsights.config. Lorsque vous êtes prêt à envoyer de nouveau les données de télémétrie au portail, supprimez les commentaires.



## <a name="monitor"></a> Affichage des données de télémétrie sur le portail Application Insights

Le portail Application Insights vous permet de consulter les données de télémétrie une fois votre application publiée. Pendant que vous procéderez au débogage, vous souhaiterez vérifier que les données de télémétrie sont correctement envoyées.

Ouvrez votre ressource Application Insights dans le [portail Azure][portal].

![Cliquez avec le bouton droit de la souris sur votre projet et ouvrez le portail Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)

Si vous n’étiez pas connecté à Azure au moment de l’ajout d’Application Insights à cette application, connectez-vous maintenant. Sélectionnez **Configurer Application Insights**. Cela vous permet de continuer à voir les données de télémétrie de votre application en temps réel après l’avoir déployée. Les données de télémétrie apparaissent dans le portail Application Insights.

### Live Stream

Pour obtenir un aperçu rapide de vos données de télémétrie lorsque vous effectuez une opération de débogage ou suite à un déploiement, utilisez Live Stream.

![Dans le panneau Vue d'ensemble, cliquez sur Live Stream](./media/app-insights-asp-net/45.png)


Live Stream est conçu pour vérifier le fonctionnement correct d’une application immédiatement après son déploiement.

Il affiche seulement les données des quelques minutes précédentes, sans les conserver.

Live Stream nécessite la version 2.1.0-beta1 ou une version ultérieure du Kit de développement logiciel (SDK).



### Rechercher : événements individuels

Ouvrez Rechercher afin d'examiner les requêtes individuelles et les événements associés.

![Dans le volet de recherche, recherchez les noms de page ou d’autres propriétés.](./media/app-insights-asp-net/21-search.png)

[En savoir plus sur la recherche](app-insights-diagnostic-search.md)

* *Aucun événement associé ?* Configurez les [exceptions de serveur](app-insights-asp-net-exceptions.md) et les [dépendances](app-insights-asp-net-dependencies.md).


### Métriques : données regroupées

Recherchez des données agrégées dans les graphiques de présentation. Au début, seuls un ou deux points s'affichent. Par exemple :

![Cliquez sur d'autres éléments pour afficher plus de données](./media/app-insights-asp-net/12-first-perf.png)

Cliquez sur un des graphiques pour afficher des métriques plus détaillées. [En savoir plus sur les mesures.][perf]

* *Pas de données utilisateur ou de page ?* - [Ajouter des données utilisateur et de page](app-insights-web-track-usage.md)

### Analytics : un puissant langage de requête

En accumulant toujours plus de données, vous pouvez exécuter des requêtes à la fois pour agréger les données et pour rechercher des instances individuelles. [Analytics]() est un outil puissant, utile pour comprendre les performances et l’utilisation, ainsi que pour mener à bien des diagnostics.

![Exemple d’Analytics](./media/app-insights-asp-net/025.png)


## Pas de données ?

* Dans Visual Studio, assurez-vous que votre application envoie correctement les données de télémétrie. Vous devriez voir des traces dans la fenêtre Sortie et dans le concentrateur de diagnostic.
* Assurez-vous que vous regardez les bonnes informations dans Azure. Connectez-vous au [portail Azure](https://portal.azure.com), cliquez sur « Parcourir », « Application Insights », puis sélectionnez votre application.
* Utilisez l'application en ouvrant différentes pages pour générer des données de télémétrie.
* Ouvrez le panneau [Rechercher][diagnostic] pour afficher les événements individuels. Parfois, les événements mettent un peu plus de temps pour parcourir le pipeline de métriques.
* Attendez quelques secondes, puis cliquez sur Actualiser.
* Consultez la rubrique [Résolution des problèmes][qna].



## Publier votre application

À présent, déployez votre application et regardez les données s'accumuler.

Utilisez [Live Stream](#live-stream) pour surveiller les premières minutes d’un déploiement ou d’un redéploiement, vous indiquant si votre application fonctionne correctement. Lorsque vous remplacez une version antérieure, notamment, vous souhaitez savoir si les performances se sont améliorées. En cas de problème, vous pourriez souhaiter revenir à l'ancienne version.


#### Vous rencontrez des problèmes sur votre serveur de builds ?

Consultez cet article de [résolution des problèmes](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [AZURE.NOTE] Si votre application génère un volume important de télémétrie (et si vous utilisez le kit de développement logiciel ASP.NET version 2.0.0-beta3 ou ultérieure), le module d'échantillonnage adaptatiif réduit automatiquement le volume qui est envoyé vers le portail en envoyant uniquement une fraction représentative des événements. Cependant, les événements liés à la même demande seront activés ou désactivés en tant que groupe, afin que vous puissiez naviguer entre les événements connexes. [En savoir plus sur l’échantillonnage.](app-insights-sampling.md)



## Étapes suivantes

- [Données utilisateur et de page](../article/application-insights/app-insights-javascript.md#selector1)
- [Exceptions](../article/application-insights/app-insights-asp-net-exceptions.md#selector1)
- [Dépendances](../article/application-insights/app-insights-asp-net-dependencies.md#selector1)
- [Availability](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)





## Pour passer aux versions ultérieures du Kit de développement logiciel (SDK)

Pour passer à la [nouvelle version du Kit de développement logiciel (SDK)](app-insights-release-notes-dotnet.md), ouvrez une nouvelle fois le gestionnaire de package NuGet et filtrez les packages qui ont été installés. Sélectionnez Microsoft.ApplicationInsights.Web et choisissez Mettre à niveau.

Si vous avez apporté des personnalisations à ApplicationInsights.config, conservez-en une copie avant d’effectuer la mise à niveau et fusionnez ensuite vos modifications dans la nouvelle version.



## <a name="video"></a>Vidéo

> [AZURE.VIDEO getting-started-with-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0518_2016-->