<properties 
	pageTitle="Diagnostic des problèmes de performances sur un site web en cours d'exécution" 
	description="Analysez les performances d'un site web sans le redéployer. Utilisation autonome ou avec le Kit de développement logiciel (SDK) Application Insights" 
	services="application-insights" 
documentationCenter=".net"
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/23/2015" 
	ms.author="awills"/>
 

# Surveillance des performances sur un site web en activité

*Application Insights est à l'état de version préliminaire.*

Vous avez une application web qui ne fonctionne pas correctement ? Diagnostiquez rapidement les exceptions et les problèmes de performances sans la régénérer ni la redéployer. Installez l'agent Application Insights sur le serveur : il vous permettra de trouver les goulots d'étranglement nuisant aux performances et d'obtenir des suivis de la pile pour les exceptions. 


#### Quand dois-je utiliser cette méthode pour configurer Application Insights ?

Cette approche est principalement destinée à diagnostiquer les problèmes de performances dans un site web IIS en activité.

Il suffit d'installer un agent dans le serveur et d'examiner les données des performances dans Application Insights.

- Vous pouvez appliquer cette méthode à une application ASP.NET hébergée sur un serveur IIS.

- Vous avez besoin d'un [compte Microsoft Azure](http://azure.com) pour afficher vos données.

- Vous avez besoin de l'accès administrateur au serveur qui exécute votre application web. 

- Vous *n'avez pas* besoin du code de l'application et ne devez pas régénérer ni redéployer cette dernière. 

- Cette méthode instrumente l'application web telle quelle. Vous n'insérez pas de code pour le suivi ou la journalisation. (Mais vous pouvez le faire plus tard si vous le souhaitez.)

Si vous voulez insérer des traces de journalisation ou de diagnostic, ne continuez pas et [ajoutez Application Insights à votre projet][greenbrown], puis redéployez-le. Pour prendre connaissance de toutes les options, lisez [Application Insights - Prise en main][start].

## Installation de l'agent Application Insights sur votre serveur web

1. Sur votre serveur web, connectez-vous avec vos informations d'identification d'administrateur.

2. Vérifiez que vous disposez de la version 5.0 ou d'une version ultérieure de [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
3. Installez l'agent Application Insights au moyen de Web Platform Installer.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-031-wpi.png)
4. Dans l'Assistant Installation, connectez-vous à Microsoft Azure.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)
5. Sélectionnez l'application web installée ou le site web à analyser, puis configurez le nom sous lequel vous voulez afficher les résultats dans le portail Application Insights. Cliquez ensuite sur le bouton Ajouter.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Vous devez normalement choisir de créer une ressource.

    Vous pouvez utiliser une ressource existante si, par exemple, vous avez déjà configuré des [tests web][availability] pour votre site.  

6. Notez que ApplicationInsights.config est inséré dans les sites web que vous voulez surveiller.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   web.config a également été légèrement modifié.

### Vous voulez (re)configurer plus tard ?

Lorsque l'Assistant est terminé, vous pouvez reconfigurer l'agent à tout moment. Vous pouvez également reconfigurer si vous avez installé l'agent et avez rencontré des problèmes lors de la configuration initiale.

![Click the Application Insights icon on the task bar](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## Affichage de vos données

Ouvrez votre compte dans Azure, accédez à Application Insights et ouvrez la ressource que vous avez créée.

![](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Les données sont affichées sous Intégrité des applications.

![](./media/app-insights-monitor-performance-live-website-now/appinsights-037-results.png)

#### Pas de données ?

  * Utilisez votre site pour créer des données.
  * Attendez quelques minutes que les données arrivent.
  * Vérifiez que le pare-feu du serveur autorise le trafic sortant sur le port 443 vers dc.services.visualstudio.com 

#### À quoi servent les rapports sur l'intégrité de l'application ?
 * [Compréhension des données et configuration des vignettes][perf]

## <a name="next"></a>Étapes suivantes


[Affichage des données][perf]

[Recherche des journaux de diagnostic][diagnostic]

[Tests web][availability]

[Configuration de la surveillance de l'utilisation][start]

[Résolution des problèmes][qna]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]




<!--HONumber=46--> 
 