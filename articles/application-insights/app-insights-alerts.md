<properties 
	pageTitle="Configuration d’alertes dans Application Insights" 
	description="Obtenez des courriers électroniques sur les pannes, les exceptions et les modifications des métriques." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="awills"/>
 
# Configuration d’alertes dans Application Insights

[Visual Studio Application Insights][start] peut vous signaler les modifications des mesures de performances ou d’utilisation de votre application.

Application Insights analyse votre application en direct sur un [large éventail de plateformes][platforms] pour vous aider à diagnostiquer les problèmes de performances et comprendre les schémas d’utilisation.

Il existe deux types d’alertes :
 
* Les **tests Web** indiquent quand votre site est indisponible sur Internet ou répond lentement. [En savoir plus][availability].
* Les **alertes de métrique** indiquent quand une métrique dépasse un seuil pendant une certaine période, comme le nombre d’échecs, la mémoire ou les affichages de page. 

Il existe une [page distincte sur les tests Web][availability], nous allons donc nous concentrer ici sur les alertes de métrique.

## Alertes de métrique

Si vous n’avez pas configuré Application Insights pour votre application, [commencez par le faire][start].

Pour obtenir un message électronique lorsqu’une métrique dépasse un seuil, démarrez dans l’Explorateur de métriques ou à partir de la vignette des Règles d’alerte sur le panneau Vue d’ensemble.

![Dans le panneau Règles d’alerte, cliquez sur Ajouter une alerte. Définissez votre application comme ressource à mesurer, fournissez un nom pour l’alerte et sélectionnez une métrique.](./media/app-insights-alerts/01-set-metric.png)

Définissez la ressource avant les autres propriétés. Ne choisissez pas les ressources de test web si vous souhaitez définir des alertes pour les métriques de performances ou d'utilisation.

Veillez à noter les unités dans lesquelles vous êtes invité à entrer la valeur seuil.

*Je ne vois pas le bouton Ajouter une alerte.* Utilisez-vous un compte professionnel ? Vous pouvez définir des alertes si vous avez un accès propriétaire ou collaborateur à cette ressource d’application. Examinez Paramètres -> Utilisateurs. [En savoir plus sur le contrôle d’accès][roles].

## Consultez vos alertes

Les alertes vous sont envoyées par courrier électronique.

Elles apparaissent également dans le journal des événements d’opérations :

![Sur le panneau Vue d’ensemble, près du bas de la page, cliquez sur « Événements de la semaine dernière »](./media/app-insights-alerts/09-alerts.png)

*Ces « événements » sont-ils liés à des événements de télémétrie ou à des événements personnalisés ?*

* Non. Ces événements opérationnels sont simplement un journal des opérations qui ont eu lieu dans cette ressource d’application. 

## Alertes de disponibilité

Vous pouvez définir des tests Web qui testent tout site Web à partir de points du monde entier. [En savoir plus][availability].

## Quelles sont les alertes à définir ?

Cela dépend de votre application. Pour commencer, il est préférable de ne pas définir un trop grand nombre de métriques. Prenez le temps d’examiner vos graphiques métriques pendant l’exécution de votre application, pour mieux comprendre comment elle se comporte normalement. Cela vous aidera à trouver des moyens d’améliorer ses performances. Définissez ensuite des alertes qui vous indiquent lorsque les métriques quittent la zone normale.

Les alertes les plus appréciées sont les suivantes :

* Les [tests Web][availability] sont importants si votre application est un site Web ou un service Web qui est visible sur l’Internet public. Ils vous indiquent si votre site tombe en panne ou répond lentement : même si le problème est plutôt dû au transporteur qu’à votre application. Ce sont des tests synthétiques, qui ne mesurent pas l’expérience réelle de vos utilisateurs.
* Les [mesures de navigateur][client], surtout les temps de chargement de la page de navigateur, sont efficaces pour les applications Web. Si votre page comporte un grand nombre de scripts, il peut être intéressant de rechercher des exceptions du navigateur. Pour obtenir ces métriques et alertes, vous devez configurer l’[analyse de la page Web][client].
* Temps de réponse de serveur et échec des demandes pour le côté serveur des applications Web. Outre la définition des alertes, gardez un œil sur ces métriques pour voir si elles varient énormément en cas de taux de demandes élevés : cela peut indiquer que votre application manque de ressources.



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

<!---HONumber=58--> 