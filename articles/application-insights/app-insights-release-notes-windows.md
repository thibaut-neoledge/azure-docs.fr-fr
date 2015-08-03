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
 
# Notes de publication pour le Kit de développement logiciel (SDK) Application Insights pour Windows Phone et Windows Store

Le [Kit de développement logiciel (SDK) Application Insights](app-insights-windows-get-started.md) envoie la télémétrie de votre application en direct à [Application Insights](http://azure.microsoft.com/services/application-insights/) afin que vous puissiez en analyser les performances et l’utilisation.


#### Pour installer le Kit de développement logiciel (SDK) dans votre application

Consultez la rubrique [Prise en main d’Application Insights pour Windows Phone et les applications du Windows Store](app-insights-windows-get-started.md).

#### Pour passer à la dernière version du Kit de développement logiciel (SDK) 

* Copiez le fichier ApplicationInsights.config pour conserver les personnalisations que vous avez effectuées.
* Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**.
* Définissez le filtre pour afficher les packages installés. 
* Sélectionnez les packages d'Application Insights installés et choisissez Mettre à niveau.
* Comparez les anciennes et nouvelles versions d’ApplicationInsights.config. Fusionnez les personnalisations que vous avez effectuées sur l'ancienne version.
* Régénérez votre solution.

## Version 1.0.0

### Kit de développement logiciel (SDK) pour application Windows

- Nouvelle initialisation pour les applications Windows. Une nouvelle classe `WindowsAppInitializer` avec la méthode `InitializeAsync()` permet l'initialisation de l’amorçage de la collection de SDK. Cette modification permet un contrôle plus précis et améliore considérablement les performances d'initialisation de l’application par rapport à la technique ApplicationInsights.config précédente.
- DeveloperMode n’est plus défini automatiquement. Pour modifier le comportement DeveloperMode, vous devez le spécifier dans le code.
- Le package NuGet n’injecte plus d’informations dans ApplicationInsights.config. Il est recommandé d'utiliser la nouvelle valeur WindowsAppInitializer lorsque vous ajoutez manuellement un package NuGet.
- ApplicationInsights.config lit uniquement `<InstrumentationKey>`, tous les autres paramètres sont ignorés dans les paramètres WindowsAppInitializer.
- Store Market sera automatiquement collecté par le Kit de développement logiciel (SDK).
- Nombreux correctifs de bogues, amélioration de la stabilité et des performances.

### Kit de développement logiciel (SDK) principal

- Le fichier ApplicationInsights.config n'est plus obligatoire. Et il n’est pas ajouté par le package NuGet. La configuration peut être entièrement spécifiée dans le code.
- Le package NuGet n’ajoutera plus de fichier cible à votre solution. Cela supprime la configuration automatique de DeveloperMode lors d’une version de débogage. DeveloperMode doit être défini manuellement dans le code.

## Version 0.17

### Kit de développement logiciel (SDK) pour application Windows

- Le Kit de développement logiciel (SDK) pour application Windows prend désormais en charge les applications universelles Windows créées par rapport à Windows 10 Technical Preview et VS 2015 RC.

### Kit de développement logiciel (SDK) principal

- TelemetryClient s'initialise par défaut avec InMemoryChannel.
- Nouvelle API ajoutée, `TelemetryClient.Flush()`. Cette méthode de visage déclenchera immédiatement un blocage du téléchargement de tous les appareils de télémétrie connectés à ce client. Cela permet de déclencher manuellement le téléchargement avant l'arrêt du processus.
- Le package NuGet a ajouté une cible .Net 4.5. Cette cible n'a aucune dépendance externe (suppression des dépendances BCL et EventSource).

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

<!---HONumber=July15_HO4-->