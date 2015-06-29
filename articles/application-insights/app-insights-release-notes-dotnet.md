<properties 
	pageTitle="Notes de publication pour Application Insights" 
	description="Les dernières mises à jour." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="sergkanz"/>
 
# Notes de publication pour le Kit de développement logiciel (SDK) Application Insights pour .NET

Le [Kit de développement logiciel (SDK) Application Insights pour .NET](app-insights-start-monitoring-app-health-usage.md) envoie la télémétrie de votre application en direct à [Application Insights](http://azure.microsoft.com/services/application-insights/) afin que vous puissiez en analyser les performances et l’utilisation.


#### Pour installer le Kit de développement logiciel (SDK) dans votre application

Consultez la rubrique [Prise en main d'Application Insights pour .NET](app-insights-start-monitoring-app-health-usage.md)

#### Pour passer à la dernière version du Kit de développement logiciel (SDK) 

* Une fois la mise à jour effectuée, vous devrez fusionner toutes les personnalisations qui ont été apportées à ApplicationInsights.config. Si vous ne savez pas si vous l’avez personnalisé, créez un nouveau projet, ajoutez Application Insights et comparez votre fichier .config avec celui du nouveau projet. Notez les différences.
* Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**.
* Définissez le filtre pour afficher les packages installés. 
* Sélectionnez **Microsoft.ApplicationInsights.Web** et choisissez **Mettre à niveau**. (Cela également mettra également à niveau tous les packages dépendants.)
* Comparez ApplicationInsights.config avec l'ancienne copie. La plupart des modifications que vous apercevez sont dues à la suppression de certains modules et à l’ajout d'autres paramétrables. Rétablissez les personnalisations que vous avez apportées à l'ancienne version.
* Régénérez votre solution.

## Version 0.17
- Suppression de la dépendance à EventSource NuGet pour les applications du framework 4.5.
- Les cookies de sessions et d'utilisateurs anonymes ne seront pas générés côté serveur. Les modules de télémétrie ```WebSessionTrackingTelemetryModule``` et ```WebUserTrackingTelemetryModule``` ne sont plus pris en charge et ont été supprimés du fichier ApplicationInsights.config. Les cookies du Kit de développement logiciel (SDK) JavaScript seront respectés.
- Le canal de persistance optimisé pour les scénarios de charge élevée est utilisé pour le Kit de développement logiciel (SDK) Web. Problème de « Spirale de la mort » résolu. Lorsque le nombre d'éléments de télémétrie dépasse largement la limite du point de terminaison, Spirale de la mort vous permet d’effectuer, après un certain temps, une nouvelle tentative, mais de manière limitée.
- Le mode Développeur est optimisé pour la production. Lorsqu’il est laissé par erreur, il ne cause pas une aussi grande surcharge qu’avant la tentative d’envoi d’informations supplémentaires.
- Le mode Développeur par défaut ne sera activé que lorsque l'application sera sous débogueur. Vous pouvez le remplacer à l'aide de l’interface ```DeveloperMode```Propriété de ```ITelemetryChannel```.

## Version 0.16 

Aperçu 28/04/2015

- Le Kit de développement logiciel (SDK) prend désormais en charge la plateforme cible DNX pour activer la surveillance des applications [.NET Core framework](http://www.dotnetfoundation.org/NETCore5).
- Les instances de ```TelemetryClient``` ne mettent plus en cache la clé d’instrumentation. Si la clé d’instrumentation n’a pas été définie explicitement dans ```TelemetryClient```, ```InstrumentationKey``` retourne la valeur nulle. Il résout un problème lorsque vous définissez ```TelemetryConfiguration.Active.InstrumentationKey``` après la collecte de certaines données de télémétrie, les modules de télémétrie comme le collecteur de dépendance, la collecte de données de demandes Web et le collecteur des compteurs de performances utilisent une nouvelle clé d’instrumentation.

## Version 0.15

- Nouvelle propriété ```Operation.SyntheticSource``` désormais disponible sur ```TelemetryContext```. Vous pouvez désormais marquer vos éléments de télémétrie comme « pas un trafic utilisateur réel » et spécifier la manière dont ce trafic a été généré. Par exemple en définissant cette propriété, vous pouvez distinguer le trafic de votre automatisation du trafic de test de charge.
- Le canal logique a été déplacé vers le NuGet distinct appelé Microsoft.ApplicationInsights.PersistenceChannel. Le canal par défaut est désormais appelé InMemoryChannel
- La nouvelle méthode ```TelemetryClient.Flush``` permet de vider des éléments de télémétrie de la mémoire tampon de manière synchrone

## Version 0.13

Aucune note de publication pour des versions antérieures.

 

<!---HONumber=58_postMigration-->