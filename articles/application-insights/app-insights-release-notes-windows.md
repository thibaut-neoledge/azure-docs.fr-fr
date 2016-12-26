---
title: Notes de publication pour Application Insights pour Windows
description: "Notes de publication du Kit de développement logiciel (SDK) (déconseillé)."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 62115aba-f806-447b-9cca-76d0e11cfbd5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/12/2016
ms.author: joshweb
ROBOTS: NOINDEX,NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 8c5324742e42a1f82bb3031af4380fc5f0241d7f
ms.openlocfilehash: 6868024441e3e0e0d2fdc44499a9b1e00364b31e

---
# <a name="release-notes-for-application-insights-sdk-for-windows-phone-and-store"></a>Notes de publication pour le Kit de développement logiciel (SDK) Application Insights pour Windows Phone et Windows Store

> [!WARNING]
> Le SDK Windows Phone et Windows Store n’est plus pris en charge. Nous vous recommandons [HockeyApp](https://www.hockeyapp.net/) pour gérer la distribution et la surveillance des applications mobiles et de bureau.
>  

Le Kit de développement logiciel (SDK) Application Insights envoie des données de télémétrie de votre application en direct à [Application Insights](https://azure.microsoft.com/services/application-insights/)afin que vous puissiez en analyser les performances et l’utilisation.

## <a name="version-111"></a>Version 1.1.1
### <a name="windows-sdk"></a>Kit de développement logiciel (SDK) Windows
* Résoudre un incident pendant une panne lors de l’utilisation du kit de développement logiciel (SDK) Silverlight de Windows Phone. Après cette modification, tout incident qui se produit plus de 2 secondes environ après l’appel à WindowsAppInitialier.InitializeAsync(...) est conservé sur disque et sera envoyé au prochain démarrage de l’application. Si un incident se produit avant le délai d’environ 2 secondes après l’appel, il sera ignoré.  
* Définir les dépendances de NuGet pour une version spécifique de Core et Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3).   

### <a name="core-sdk"></a>Kit de développement logiciel (SDK) principal
* Cre est géré dans github. Les notes de publication des versions ultérieures du Kit de développement logiciel (SDK) Core sont disponibles [dans github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## <a name="version-11"></a>Version 1.1
### <a name="core-sdk"></a>Kit de développement logiciel (SDK) principal
* Le kit de développement logiciel (SDK) inaugure le nouveau type de données de télémétrie ```DependencyTelemetry``` qui contient des informations sur l’appel de dépendance de l’application.
* La nouvelle méthode ```TelemetryClient.TrackDependency``` permet d’envoyer des informations sur les appels de dépendance de l’application.

## <a name="version-100"></a>Version 1.0.0
### <a name="windows-app-sdk"></a>Kit de développement logiciel (SDK) pour application Windows
* Nouvelle initialisation pour les applications Windows. La nouvelle classe `WindowsAppInitializer` associée à la méthode `InitializeAsync()` permet l’initialisation de l’amorçage de la collection du kit de développement logiciel (SDK). Cette modification permet un contrôle plus précis et améliore considérablement les performances d'initialisation de l’application par rapport à la technique ApplicationInsights.config précédente.
* DeveloperMode n’est plus défini automatiquement. Pour modifier le comportement DeveloperMode, vous devez le spécifier dans le code.
* Le package NuGet n’injecte plus d’informations dans ApplicationInsights.config. Il est recommandé d'utiliser la nouvelle valeur WindowsAppInitializer lorsque vous ajoutez manuellement un package NuGet.
* ApplicationInsights.config lit uniquement `<InstrumentationKey>`, tous les autres paramètres sont ignorés en faveur des paramètres WindowsAppInitializer.
* Store Market sera automatiquement collecté par le Kit de développement logiciel (SDK).
* Nombreux correctifs de bogues, amélioration de la stabilité et des performances.

### <a name="core-sdk"></a>Kit de développement logiciel (SDK) principal
* Le fichier ApplicationInsights.config n'est plus obligatoire. Et il n’est pas ajouté par le package NuGet. La configuration peut être entièrement spécifiée dans le code.
* Le package NuGet n’ajoutera plus de fichier cible à votre solution. Cela supprime la configuration automatique de DeveloperMode lors d’une version de débogage. DeveloperMode doit être défini manuellement dans le code.

## <a name="version-017"></a>Version 0.17
### <a name="windows-app-sdk"></a>Kit de développement logiciel (SDK) pour application Windows
* Le Kit de développement logiciel (SDK) pour application Windows prend désormais en charge les applications universelles Windows créées par rapport à Windows 10 Technical Preview et VS 2015 RC.

### <a name="core-sdk"></a>Kit de développement logiciel (SDK) principal
* TelemetryClient s'initialise par défaut avec InMemoryChannel.
* Nouvelle API ajoutée, `TelemetryClient.Flush()`. Cette méthode de visage déclenchera immédiatement un blocage du téléchargement de tous les appareils de télémétrie connectés à ce client. Cela permet de déclencher manuellement le téléchargement avant l'arrêt du processus.
* Le package NuGet a ajouté une cible .Net 4.5. Cette cible n'a aucune dépendance externe (suppression des dépendances BCL et EventSource).

## <a name="version-016"></a>Version 0.16
Aperçu 28/04/2015

* Le Kit de développement logiciel (SDK) prend désormais en charge la plateforme cible DNX pour activer la surveillance des applications [.NET Core framework](http://www.dotnetfoundation.org/NETCore5) .
* Les instances de ```TelemetryClient``` ne mettent plus en cache la clé d’instrumentation. Si la clé d’instrumentation n’a pas été définie explicitement dans ```TelemetryClient```, ```InstrumentationKey``` retourne la valeur nulle. Il résout un problème lorsque vous définissez ```TelemetryConfiguration.Active.InstrumentationKey``` après la collecte de certaines données de télémétrie, les modules de télémétrie comme le collecteur de dépendance, la collecte de données de demandes Web et le collecteur des compteurs de performances utilisent une nouvelle clé d’instrumentation.

## <a name="version-015"></a>Version 0.15
* Nouvelle propriété ```Operation.SyntheticSource``` désormais disponible sur ```TelemetryContext```. Vous pouvez désormais marquer vos éléments de télémétrie comme « pas un trafic utilisateur réel » et spécifier la manière dont ce trafic a été généré. Par exemple en définissant cette propriété, vous pouvez distinguer le trafic de votre automatisation du trafic de test de charge.
* Le canal logique a été déplacé vers le NuGet distinct appelé Microsoft.ApplicationInsights.PersistenceChannel. Le canal par défaut est désormais appelé InMemoryChannel
* La nouvelle méthode ```TelemetryClient.Flush``` permet de vider des éléments de télémétrie de la mémoire tampon de manière synchrone

## <a name="version-013"></a>Version 0.13
Aucune note de publication pour des versions antérieures. 




<!--HONumber=Nov16_HO4-->


