<properties
   pageTitle="Microsoft Azure Service Fabric - Comment surveiller et diagnostiquer des services localement"
   description="Cet article décrit comment vous pouvez surveiller et diagnostiquer vos services écrits à l'aide de Microsoft Azure Service Fabric sur un ordinateur de développement local."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/22/2015"
   ms.author="kunalds"/>


# Surveillance et diagnostic des services dans une configuration de développement d'ordinateur local
L'analyse, la détection, le diagnostic et la résolution des problèmes permettent aux services de fonctionner avec une interruption minimale de l'expérience utilisateur. Bien qu'essentielle dans un environnement de production réel déployé, l'efficacité dépend de l'adoption d'un modèle semblable pendant le développement des services pour garantir son fonctionnement lors du passage à une configuration réelle. Service Fabric facilite pour les développeurs de service l'implémentation de diagnostics qui peuvent fonctionner parfaitement aussi bien sur une configuration de développement d'ordinateur local unique que sur une configuration réelle de cluster de production.

## Suivi et journalisation
Le [Suivi d'événements pour Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) est la technologie recommandée pour le suivi des messages dans Service Fabric. Les raisons sont les suivantes :

* ETW est rapide. Il a été créé en tant que technologie de suivi avec un impact minimal sur le temps d'exécution de votre code.
* Le suivi ETW fonctionne parfaitement dans des environnements de développement local et également dans les configurations de cluster réel. Cela signifie que vous n'êtes pas obligé de réécrire votre code de suivi quand vous êtes prêt à déployer votre code dans un cluster réel.
* Le code système de Service Fabric utilise également ETW pour le suivi interne. Cela vous permet d'afficher les traces de votre application entremêlées avec celles du système Service Fabric, il est alors plus facile de comprendre les séquences et les relations entre votre code d'application et les événements dans le système sous-jacent.
* Les outils Visual Studio de Service Fabric prennent en charge l'affichage des événements ETW.


## Afficher les événements système de Service Fabric dans Visual Studio

Service Fabric émet des événements ETW pour aider les développeurs d'applications à comprendre ce qui se passe dans la plateforme. Pour afficher ces événements, procédez comme suit :

1. Vous devez d'abord installer les composants suivants :
   * Visual Studio 2015
   * SDK Service Fabric

2. Lancez Visual Studio en tant qu'administrateur.

3. Créez un projet (ou ouvrez un projet existant) pour un acteur/service avec ou sans état.

  ![Créer une application Service Fabric](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/CreateServiceFabricProject.png)

  ![Créer un service Service Fabric](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/CreateServiceFabricProject-2.png)

4. Appuyez sur F5 pour déboguer l'application. Les événements Service Fabric doivent s'afficher dans la fenêtre Événements de diagnostic. Chaque événement comporte des informations de métadonnées standard qui vous indiquent le nœud, l'application et le service d'où il provient. Vous pouvez également filtrer la liste des événements à l'aide de la zone « Filtrer les événements » en haut des fenêtres. Vous pouvez, par exemple, filtrer sur le Nom du nœud ou le Nom du service.

  ![Observateur d'événements de diagnostic de Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

5. Si la fenêtre Diagnostics d'événements ne s'affiche pas automatiquement, accédez à l'onglet Explorateur de serveurs dans Visual Studio, cliquez avec le bouton droit sur le cluster Service Fabric et choisissez « Afficher les événements de diagnostic » dans le menu contextuel.

  ![Ouvrir l'Observateur d'événements de diagnostic de Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/ServerExViewDiagEvents.png)

## Ajouter vos propres traces personnalisées au code d'application
Les modèles de projet Visual Studio de Service Fabric contiennent des exemples de code. Le code indique comment ajouter les traces ETW de code d'application personnalisé qui s'affichent dans la visionneuse ETW de Visual Studio avec les traces système de Service Fabric. L'avantage de cette méthode est que les métadonnées sont automatiquement ajoutées aux traces et la visionneuse de diagnostics de Visual Studio est déjà configurée pour les afficher.

Pour les projets créés à partir des **modèles de service** (sans état ou avec état) :

1. Ouvrez le fichier **Service.cs**. L'appel de `ServiceEventSource.Current.Message` dans la méthode *RunAsync* est un exemple de suivi ETW personnalisé à partir du code d'application.
2. Dans le fichier **ServiceEventSource.cs**, les surcharges de la méthode `ServiceEventSource.Message` indiquent une façon d'écrire des suivis ETW personnalisés.

Pour les projets créés à partir des **modèles d'acteur** (sans état ou avec état) :

1. Ouvrez le fichier **« Nom\_projet ».cs** où *Nom\_projet* est le nom que vous avez choisi pour votre projet Visual Studio.  
2. Recherchez le code `ActorEventSource.Current.ActorMessage(this, "Doing Work");` dans la méthode *DoWorkAsync*. Il s'agit d'un exemple de suivi ETW personnalisé à partir du code d'application.  
3. Dans le fichier **ActorEventSource.cs**, les surcharges de la méthode `ActorEventSource.ActorMessage` indiquent une façon d'écrire des suivis ETW personnalisés.

Après avoir ajouté le suivi ETW personnalisé à votre code de service, vous pouvez générer, déployer et exécuter l'application à nouveau pour afficher vos événements dans la visionneuse de diagnostics. Si vous déboguez l'application en appuyant sur F5, la visionneuse de diagnostics s'ouvre automatiquement.

##Bientôt disponible
Le même code de suivi que vous avez ajouté à votre application ci-dessus pour les diagnostics locaux fonctionne avec les outils que vous pouvez utiliser pour afficher ces événements quand vous exécutez le même code sur un cluster Azure. Les informations à ce sujet seront bientôt disponibles.

## Étapes suivantes

* [Présentation de l'intégrité de Service Fabric](service-fabric-health-introduction.md)
* [Configuration d'Application Insights](service-fabric-diagnostics-application-insights-setup.md)
* [Diagnostics et surveillance des performances pour Azure Service Fabric Actors](service-fabric-reliable-actors-diagnostics.md)
* [Diagnostics de service fiable avec état](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=August15_HO6-->