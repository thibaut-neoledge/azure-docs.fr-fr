<properties 
	pageTitle="Diagnostic des problèmes de performances sur un site web en cours d'exécution" 
	description="Analysez les performances d'un site web sans le redéployer. Utilisation autonome ou avec le Kit de développement logiciel (SDK) Application Insights pour obtenir des données de télémétrie relatives aux dépendances." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="awills"/>
 

# Installation d’Application Insights Status Monitor pour surveiller les performances des sites web

*Application Insights est à l'état de version préliminaire.*

Le Status Monitor de Visual Studio Application Insights vous permet de diagnostiquer les problèmes liés aux exceptions et aux performances dans les applications web s’exécutant sur n’importe quel serveur IIS. Il vous suffit de l’installer sur votre serveur web IIS pour qu’il soit associé aux applications web ASP.NET dans le but d’envoyer des données au portail Application Insights à des fins de recherche et d’analyse. Vous pouvez l’installer sur n’importe quel serveur physique ou virtuel pour lequel vous disposez d’un accès administrateur.

![Exemples de graphiques](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Vous avez le choix entre trois façons d’appliquer Application Insights à vos applications web IIS :

* **En cours de création :** [ajoutez le kit de développement logiciel (SDK) Application Insights][greenbrown] au code de votre application web. Cela permet :
 * d’obtenir un ensemble de diagnostics standard et de données de télémétrie liées à l’utilisation. 
 * Et de pouvoir utiliser l’[API Application Insights][api] si vous souhaitez écrire votre propre télémétrie pour effectuer un suivi de l’utilisation ou diagnostiquer les problèmes.
* **En cours d’exécution :** utilisez Status Monitor pour l’associer à votre application web sur le serveur.
 * Surveillance des applications web déjà exécutées sans avoir à les générer ou les publier de nouveau.
 * Ensemble de diagnostics standard et de données de télémétrie liées à l’utilisation.
 * Diagnostics de dépendance  : recherche des erreurs ou des faibles performances là où votre application utilise d’autres composants tels que des bases de données, des API REST ou d’autres services.
 * Résolution des problèmes à l’aide des données de télémétrie.
* **En cours d’exécution et de création :** compilez le Kit de développement logiciel (SDK) dans le code de votre application web et exécutez Status Monitor sur votre serveur web. Cette méthode associe les avantages des deux autres méthodes :
 * Ensemble de diagnostics standard et données de télémétrie liées à l’utilisation.
 * Diagnostics de dépendance.
 * Élaboration d’une télémétrie personnalisée à l’aide de l’API.
 * Résolution des problèmes à l’aide du Kit de développement logiciel (SDK) et de la télémétrie.



> [AZURE.TIP]S’agit-il d’une application [Azure App Service Web App](../app-service-web/websites-learning-map.md) ? [Ajoutez le kit de développement logiciel (SDK) Application Insights][greenbrown], puis [l’extension Application Insights](../insights-perf-analytics.md) à partir du panneau de configuration de l’application dans Azure.


## Installez Application Insights Status Monitor sur votre serveur web IIS

1. Cette opération nécessite un abonnement [Microsoft Azure](http://azure.com). 

1. Sur votre serveur web IIS, connectez-vous avec vos informations d’identification d’administrateur.
2. Téléchargez et exécutez le [programme d’installation Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).

4. Dans l'Assistant Installation, connectez-vous à Microsoft Azure.

    ![Connectez-vous à Azure avec les informations d’identification de votre compte Microsoft.](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

5. Sélectionnez l’application web installée ou le site web à surveiller, puis configurez la ressource dans laquelle vous voulez afficher les résultats dans le portail Application Insights.

    ![Choisissez une application et une ressource.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Normalement, vous devez choisir de configurer une nouvelle ressource et un nouveau [groupe de ressources][roles].

    Vous pouvez utiliser une ressource existante si vous avez déjà configuré des [tests web][availability] pour votre site ou une [surveillance du client web][client].

6. Redémarrez IIS.

    ![Cliquez sur Redémarrer en haut de la boîte de dialogue.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    Votre service web sera interrompu pendant une courte période.

6. Notez que ApplicationInsights.config est inséré dans les applications web que voulez surveiller.

    ![Recherchez le fichier .config et les fichiers de code de l’application web.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   web.config a également été légèrement modifié.

### Vous voulez (re)configurer plus tard ?

Lorsque l'Assistant est terminé, vous pouvez reconfigurer l'agent à tout moment. Vous pouvez également reconfigurer si vous avez installé l'agent et avez rencontré des problèmes lors de la configuration initiale.

![Click the Application Insights icon on the task bar](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## Affichage des données de télémétrie relatives aux performances

Connectez-vous au [portail Azure en version préliminaire](http://portal.azure.com), accédez à Application Insights et ouvrez la ressource que vous avez créée.

![Sélectionnez Parcourir, Application Insights, puis votre application.](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Ouvrez le panneau des performances pour afficher les dépendances et les autres données.

![Performances](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Cliquez sur n’importe quel graphique pour afficher plus de détails.


![](./media/app-insights-monitor-performance-live-website-now/appinsights-038-dependencies.png)

#### Dépendances

Les graphiques libellés HTTP, SQL et AZUREBLOB indiquent les temps de réponse et le nombre d’appels de dépendances : autrement dit, les services externes utilisés par votre application.



#### Compteurs de performances

Cliquez sur n’importe quel graphique du compteur de performance pour changer les données affichées. Vous pouvez aussi ajouter un nouveau graphique.
 
#### Exceptions

![Cliquez sur le graphique d’exceptions de serveur.](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Vous pouvez accéder aux exceptions spécifiques (des sept derniers jours) et obtenir les arborescences des appels de procédure et des données de contexte.


### Vous n’obtenez aucune donnée de télémétrie ?

  * Utilisez votre site pour créer des données.
  * Attendez quelques minutes que les données arrivent, puis cliquez sur Actualiser.
  * Ouvrez Recherche de diagnostic (vignette de recherche) pour afficher chaque événement. Les événements sont souvent visibles dans Recherche de diagnostic avant que les données agrégées n’apparaissent dans les graphiques.
  * Ouvrez Status Monitor et sélectionnez votre application dans le volet gauche. Vérifiez la présence de messages de diagnostic pour cette application dans la section « Notifications de configuration » :
  
  ![](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * Vérifiez que le pare-feu de votre serveur autorise le trafic sortant sur le port 443 vers dc.services.visualstudio.com. 
  * Si un message relatif à des « autorisations insuffisantes » s’affiche sur le serveur, procédez comme suit :
  * Dans le Gestionnaire des services Internet, sélectionnez votre pool d’applications, ouvrez Paramètres avancés, et sous Modèle de processus, notez l’identité.
  * Dans le panneau de configuration relatif à la gestion de l’ordinateur, ajoutez cette identité au groupe Utilisateurs de l’Analyseur de performances.
  * Consultez la rubrique [Résolution des problèmes][qna].

## Configuration requise

Prise en charge du système d’exploitation pour Application Insights Status Monitor sur le serveur :

- Windows Server 2008
- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

avec dernier Service Pack et .NET Framework 4.0 et 4.5

Windows 7, 8 et 8.1 côté client, avec également .NET Framework 4.0 et 4.5

Prise en charge d’IIS : IIS 7, 7.5, 8, 8.5 (IIS requis)

## <a name="next"></a>Étapes suivantes

* [Créez des tests web][availability] pour vous assurer que votre site reste actif.
* [Recherchez les événements et les journaux][diagnostic] pour diagnostiquer les problèmes.
* [Ajoutez la télémétrie de client web][usage] pour afficher les exceptions à partir du code de la page web et vous permettre d’insérer un suivi des appels.
* [Ajoutez le kit de développement logiciel (SDK) Application Insights à votre code de service web][greenbrown] afin de pouvoir insérer un suivi des appels et des appels de journaux dans le code du serveur.

## Vidéo

#### Analyse des performances

[AZURE.VIDEO app-insights-performance-monitoring]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO3-->