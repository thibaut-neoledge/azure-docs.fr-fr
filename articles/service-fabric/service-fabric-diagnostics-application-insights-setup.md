<properties
   pageTitle="Configuration d'Application Insights pour votre application Service Fabric"
   description="Recevez des événements Service Fabric pour votre application dans Application Insights."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/22/2015"
   ms.author="mattrow"/>

# Configuration d'Application Insights pour votre application Service Fabric
 Cet article vous guidera dans l'activation d'Application Insights pour votre application Service Fabric.

## Composants requis

Cet article suppose que vous avez déjà créé une application Service Fabric dans Visual Studio. Pour savoir comment procéder, [cliquez ici](service-fabric-reliable-services-quick-start.md).

## Installation du package Nuget
Le SDK Service Fabric inclut une version préliminaire de notre package NuGet Microsoft.ServiceFabric.Telemetry.ApplicationInsights. Ce package relie les événements EventSource de Service Fabric et d'Application Insights pour vous permettre une instrumentation automatisée de votre application Service Fabric. Il est mis à jour avec les nouveaux événements qui sont automatiquement émis par votre application.

Vous pouvez installer le package en procédant comme suit :

1. Ouvrez le gestionnaire de package NuGet de votre application Service Fabric. Pour ce faire, cliquez avec le bouton droit sur votre projet dans Visual Studio et sélectionnez « Gérer les packages NuGet... ».
2. Vous devez sélectionner « Microsoft Azure Service Fabric » comme source du package pour répertorier les packages inclus dans le SDK Service Fabric. ![Gestionnaire de package NuGet VS2015](media/service-fabric-diagnostics-application-insights-setup/AI-nuget-package-manager.jpg)
3. Sélectionnez le package Microsoft.ServiceFabric.Telemetry.ApplicationInsights à gauche.
4. Cliquez sur Installer pour commencer le processus d'installation.
5. Lisez et acceptez le CLUF.

## Activation des événements Service Fabric
Pour recevoir automatiquement les événements Service Fabric dans Application Insights, vous devez activer notre écouteur. Pour ce faire, insérez la ligne de code suivante dans votre application.

```csharp
    Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
```
 
### Exemple pour StatefulActor\\Program.cs :

```csharp
    public static void Main(string[] args)
    {
        Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(StatefulActor));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
```

Vous pouvez en savoir plus sur les événements émis par le runtime des acteurs fiables [ici](service-fabric-reliable-actors-diagnostics.md) et par le runtime des services fiables [ici](service-fabric-reliable-services-diagnostics.md).

Notez que pour obtenir des appels de méthode du runtime des acteurs fiables, EventLevel.Verbose doit être utilisé (comme indiqué dans les exemples ci-dessus).

## Configuration d'Application Insights
La clé d'instrumentation permet de relier votre application Service Fabric à votre ressource Application Insights. Pour en savoir plus sur l'obtention de votre clé d'instrumentation, consultez le [guide d'Application Insights](../app-insights-create-new-resource.md#create-an-application-insights-resource). Sélectionnez « Autres » pour le type d'application lors de la création des ressources.

![Sélectionner Autre pour le type d'application AI](media/service-fabric-diagnostics-application-insights-setup/AI-app-type-other.JPG)

Dès que vous avez votre clé d'instrumentation, vous pouvez l'insérer dans le fichier ApplicationInsights.config comme suit :

```xml
    <InstrumentationKey>INSERT YOUR KEY HERE</InstrumentationKey>
```

## Affichage des données
Vous pouvez [personnaliser le panneau Application Insights](../app-insights-metrics-explorer.md) en fonction de vos besoins. La plupart des événements Service Fabric s'affichent en « Événements personnalisés », alors que les appels de méthode des acteurs Service Fabric et les appels du service RunAsync() apparaissent comme des demandes. La modélisation de ces événements sous forme de demandes vous permet d'utiliser la dimension « nom de la demande » et la mesure « durée de la demande » pour la génération de graphiques. De nouveaux graphiques, mesures et événements que vous pouvez exploiter seront ajoutés à l'avenir.

## Étapes suivantes
Découvrez comment utiliser Application Insights pour instrumenter vos applications Service Fabric.

- [Prise en main d'Application Insights](../app-insights-get-started.md)
- [En savoir plus sur la création de vos propres événements et mesures personnalisés](../app-insights-custom-events-metrics-api.md)
 

<!---HONumber=July15_HO2-->