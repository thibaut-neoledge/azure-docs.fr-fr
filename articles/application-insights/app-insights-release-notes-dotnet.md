<properties 
	pageTitle="Notes de publication pour Application Insights pour .NET" 
	description="Les dernières mises à jour pour le Kit de développement logiciel (SDK) .NET." 
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
	ms.date="08/06/2015" 
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

## Version 2.0.0-beta2
- Prise en charge ajoutée d'ITelemetryProcessor et possibilité de configurer via le code ou config. [Permet le filtrage personnalisé dans le kit de développement logiciel](https://azure.microsoft.com/documentation/articles/app-insights-api-telemetry-processors/#telemetry-processors)
- Suppression des initialiseurs de contexte Utilisez maintenant les [initialiseurs de télémétrie](https://azure.microsoft.com/documentation/articles/app-insights-api-telemetry-processors/#telemetry-initializers).
- Mise à niveau d'Application Insights pour .Net framework 4.6. 
- Les noms d'événements personnalisés peuvent maintenant comprendre jusqu'à 512 caractères.
- La propriété ```OperationContext.Name``` a été renommée en ```RootName```.
- La propriété ```RequestTelemetry.Id``` a été supprimée.
- Les propriétés ```Id``` et ```Context.Operation.Id``` de RequestTelemetry ne sont pas initialisées lors de la création d'une nouvelle RequestTelemetry.
- ```RequestTelemetry.Name``` n'est plus initialisé. ```RequestTelemetry.Context.Operation.Name``` sera utilisé à sa place.
- Dans l'analyse de requête, le code de réponse 401 fait partie de la négociation d'authentification normale et entraîne la réussite de la requête.
- Correction du verrouillage du thread d'interface utilisateur lors de l'initialisation d'InMemoryChannel (canal par défaut) à partir du thread d'interface utilisateur. Les problèmes de blocages de l'interface dans les applications WPF sont ainsi résolus.
 
## Version 2.0.0-beta1
- TrackDependency produira une instance JSON valide alors que tous les champs requis n’auront pas été indiqués.
- La propriété redondante ```RequestTelemetry.ID``` est maintenant simplement un proxy pour ```RequestTelemetry.Operation.Id```.
- Nouvelle interface ```ISupportSampling``` et mise en œuvre explicite de celle-ci par la plupart des types d'éléments de données.
- La propriété ```Count``` dans DependencyTelemetry est marquée comme obsolète. Utilisez plutôt ```SamplingPercentage```.
- Introduction de ```CloudContext```, et déplacement des propriétés ```RoleName``` et ```RoleInstance``` vers ce point depuis ```DeviceContext```.
- Nouvelle propriété ```AuthenticatedUserId``` dans ```UserContext``` pour spécifier l'identité des utilisateurs non authentifiés.
- Ajout de `Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer`, `Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer` qui initialise le contexte de l'utilisateur authentifié tel que défini par le kit de développement logiciel Javascript.
- Ajout de `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor` et prise en charge de l'échantillonnage à débit fixe.
- Ajout de `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.TelemetryChannelBuilder` pour autoriser la création d'un `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` avec un jeu de `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor`.

## Version 1.2

- Les initialiseurs de télémétrie qui n'ont pas de dépendances dans les bibliothèques ASP.NET ont été déplacés de l'emplacement `Microsoft.ApplicationInsights.Web` vers le nouveau nuget de dépendance `Microsoft.ApplicationInsights.WindowsServer`.
- `Microsoft.ApplicationInsights.Web.dll` a été renommé dans `Microsoft.AI.Web.dll`.
- Le nuget `Microsoft.ApplicationInsights.Web.TelemetryChannel` a été renommé dans `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`. L'assembly `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` a été renommé dans `Microsoft.AI.ServerTelemetryChannel.dll`. La classe `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` a été renommée dans `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`.
- Tous les espaces de noms qui font partie d'un kit de développement logiciel Web ont été modifiés de manière à exclure la partie `Extensibility`. Cela inclut tous les initialiseurs de télémétrie dans ApplicationInsights.config et le module `ApplicationInsightsWebTracking` dans le fichier web.config.
- Les dépendances collectées à l'aide de l'agent d’instrumentation de runtime (activé via l'extension Status Monitor ou du site Web Azure) ne seront pas marquées comme asynchrones s'il n’y a aucun HttpContext.Current sur le thread.
- La propriété `SamplingRatio` de `DependencyTrackingTelemetryModule` n'a aucun effet et est marquée comme obsolète.
- L'assembly `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector` a été renommé dans `Microsoft.AI.PerfCounterCollector`
- Plusieurs résolutions de bogues mineurs dans les kits de développement logiciel Web ou d’appareils


## Version 1.1

- Un nouveau type de télémétrie `DependencyTelemetry` a été ajouté. Il peut être utilisé pour envoyer des informations sur les appels de dépendance à partir de l'application (par exemple des appels SQL, HTTP, etc.).
- Une nouvelle méthode de surcharge `TelemetryClient.TrackDependency` a été ajoutée. Elle vous permet d'envoyer des informations sur les appels de dépendance.
- Une valeur NullReferenceException fixe est envoyée par le module de diagnostics lorsque TelemetryConfiguration.CreateDefault est utilisé.

## Version 1.0

- Les initialiseurs et les modules de télémétrie ont été déplacés des sous-espaces de noms distincts vers l'espace de noms racine `Microsoft.ApplicationInsights.Extensibility.Web`.
- Le préfixe « Web » a été supprimé des noms des initialiseurs et des modules de télémétrie, car il est déjà inclus dans le nom de l'espace de noms `Microsoft.ApplicationInsights.Extensibility.Web`.
- `DeviceContextInitializer` a été déplacé de l'assembly `Microsoft.ApplicationInsights` vers l'assembly `Microsoft.ApplicationInsights.Extensibility.Web` et converti en un `ITelemetryInitializer`.
- Les noms d'assembly et d'espaces de noms `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` ont été remplacés par `Microsoft.ApplicationInsights.Extensibility.DependencyCollector` par souci de cohérence avec le nom du package NuGet.
- `RemoteDependencyModule` a été renommé en `DependencyTrackingTelemetryModule`.
- `CustomPerformanceCounterCollectionRequest` a été renommé en `PerformanceCounterCollectionRequest`.

## Version 0.17
- Suppression de la dépendance à EventSource NuGet pour les applications du framework 4.5.
- Les cookies de sessions et d'utilisateurs anonymes ne seront pas générés côté serveur. Pour implémenter le suivi de l'utilisateur et de la session pour les applications web, l’instrumentation avec JS SDK est désormais obligatoire – les cookies du SDK JavaScript sont toujours respectés. Les modules de télémétrie ```WebSessionTrackingTelemetryModule``` et ```WebUserTrackingTelemetryModule``` ne sont plus pris en charge et ont été supprimés du fichier ApplicationInsights.config. Notez que cette modification peut provoquer un changement significatif du nombre d'utilisateurs et de sessions car seules les sessions créées par l'utilisateur sont désormais comptabilisées.
- OSVersion n'est plus renseigné par le Kit de développement logiciel (SDK) par défaut. Lorsqu’elles sont vides, les valeurs OS et OSVersion sont calculées par le pipeline Application Insights, en fonction de l'agent utilisateur. 
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

 

<!---HONumber=Nov15_HO2-->