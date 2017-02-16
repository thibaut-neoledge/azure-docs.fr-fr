---
title: "Collecter les journaux directement à partir d’un processus de service Azure Service Fabric | Microsoft Azure"
description: "Décrit les applications Service Fabric permettant d’envoyer les journaux directement vers un emplacement central comme Azure Application Insights ou Elasticsearch, sans faire appel à un agent Azure Diagnostics."
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: rwike77
editor: 
ms.assetid: ab92c99b-1edd-4677-8c28-4e591d909b47
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/18/2017
ms.author: karolz@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: d7f7b157d8d6fb54259c8f23d5005509f4eb7872
ms.openlocfilehash: 22acb6afbfbfff753e71b5e821385798cc76ffdd


---
# <a name="collect-logs-directly-from-an-azure-service-fabric-service-process"></a>Collecter les journaux directement à partir d’un processus de service Azure Service Fabric
## <a name="in-process-log-collection"></a>Collecte de journaux dans le processus
La collecte des journaux d’application à l’aide de l’[extension Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md) s’avère efficace pour **Azure Service Fabric** si l’ensemble de sources et de destinations des journaux est restreint, ne change pas souvent et s’il existe un mappage simple entre les sources et les destinations. Si ce n’est pas le cas, une autre solution consiste à configurer les services de sorte qu’ils envoient leurs journaux directement vers un emplacement central. Ce processus, appelé **collecte de journaux dans le processus**, présente plusieurs avantages potentiels :

* *Simplicité de configuration et de déploiement*

    * La configuration de la collecte des données de diagnostic s’inscrit simplement dans le cadre de la configuration du service. Elle reste toujours facilement « synchronisée » avec le reste de l’application.
    * Vous pouvez facilement réaliser une configuration par application ou par service.
        * La collecte de journaux basée sur un agent implique généralement d’effectuer séparément le déploiement et la configuration de l’agent de diagnostic, ce qui représente une tâche administrative supplémentaire potentiellement source d’erreurs. Souvent, une seule instance de l’agent est autorisée par machine virtuelle (nœud) et la configuration de l’agent est partagée entre l’ensemble des applications et des services exécutés sur ce nœud. 

* *Flexibilité*
   
    * L’application peut envoyer les données partout où elles sont nécessaires, tant qu’il existe une bibliothèque cliente capable de prendre en charge le système de stockage ciblé. Vous pouvez ajouter de nouvelles destinations selon vos besoins.
    * Vous pouvez également implémenter des règles de capture, de filtrage et d’agrégation de données complexes.
    * La collecte de journaux basée sur un agent est souvent limitée par les récepteurs de données pris en charge par l’agent. Certains agents sont extensibles.

* *Accès aux données d’application internes et contexte*
   
    * Le sous-système de diagnostic exécuté à l’intérieur du processus d’application/service peut facilement enrichir les traces d’informations contextuelles.
    * Avec la collecte de journaux basée sur un agent, les données doivent être envoyées à un agent par le biais d’un mécanisme de communication entre processus, tel que le suivi d’événements pour Windows. Ce mécanisme peut imposer des limites supplémentaires.

Il est possible de combiner ces deux méthodes de collecte et de tirer parti de leurs avantages respectifs. En effet, cela peut constituer la meilleure solution pour de nombreuses applications. La collecte basée sur un agent est une solution idéale pour collecter les journaux liés au cluster dans son ensemble et aux nœuds de cluster individuels. Elle s’avère beaucoup plus fiable que la collecte de journaux dans le processus pour diagnostiquer les problèmes de démarrage de service et les incidents. Par ailleurs, lorsque de nombreux services sont exécutés à l’intérieur d’un cluster Service Fabric, la collecte de journaux dans le processus par chaque service donne lieu à de nombreuses connexions sortantes à partir du cluster. Ce grand nombre de connexions sortantes affecte à la fois le sous-système réseau et la destination des journaux. Un agent tel qu’[**Azure Diagnostics** ](../cloud-services/cloud-services-dotnet-diagnostics.md) peut collecter les données de plusieurs services et envoyer toutes ces données via un nombre réduit de connexions, ce qui améliore le débit. 

Cet article montre comment configurer une collecte de journaux dans le processus à l’aide de la [**bibliothèque open source EventFlow**](https://github.com/Azure/diagnostics-eventflow). D’autres bibliothèques peuvent être utilisées dans le même but, mais EventFlow présente l’avantage d’avoir été conçu spécifiquement pour la collecte de journaux dans le processus et pour prendre en charge les services Service Fabric. Nous utilisons [**Azure Application Insights** ](https://azure.microsoft.com/services/application-insights/) comme destination des journaux. D’autres destinations telles qu’[**Event Hubs**](https://azure.microsoft.com/services/event-hubs/) ou [**Elasticsearch**](https://www.elastic.co/products/elasticsearch) sont également prises en charge. Il suffit simplement d’installer le package NuGet approprié et de configurer la destination dans le fichier de configuration EventFlow. Pour plus d’informations sur les destinations de journaux autres qu’Application Insights, consultez la [documentation relative à EventFlow](https://github.com/Azure/diagnostics-eventflow).

## <a name="adding-eventflow-library-to-a-service-fabric-service-project"></a>Ajout de la bibliothèque EventFlow à un projet de service Service Fabric
Les fichiers binaires EventFlow sont disponibles sous la forme d’un ensemble de packages NuGet. Pour ajouter EventFlow à un projet de service Service Fabric, cliquez sur le projet dans l’Explorateur de solutions et sélectionnez Gérer les packages NuGet. Basculez vers l’onglet Parcourir et recherchez « `Diagnostics.EventFlow` » :

![Packages NuGet EventFlow dans l’interface utilisateur du gestionnaire de package NuGet Visual Studio][1]

Le service qui héberge EventFlow doit inclure les packages appropriés en fonction de la source et de la destination des journaux d’application. Ajoutez les packages suivants : 

* `Microsoft.Diagnostics.EventFlow.Input.EventSource` 
    * (pour capturer les données à partir de la classe EventSource du service et des EventSources standard tels que *Microsoft-ServiceFabric-Services* et *Microsoft-ServiceFabric-Actors*)
* `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights` 
    * (nous allons envoyer les journaux à une ressource Azure Application Insights)  
* `Microsoft.Diagnostics.EventFlow.ServiceFabric` 
    * (permet d’initialiser le pipeline EventFlow à partir de la configuration du service Service Fabric et de signaler tout problème lié à l’envoi des données de diagnostic sous forme de rapports d’intégrité Service Fabric)

> [!NOTE]
> Le package `Microsoft.Diagnostics.EventFlow.Input.EventSource` nécessite le ciblage de .NET Framework 4.6 ou version ultérieure par le projet de service. Veillez à définir l’infrastructure cible appropriée dans les propriétés du projet avant d’installer ce package. 

Une fois tous les packages installés, l’étape suivante consiste à configurer et à activer EventFlow dans le service.

## <a name="configuring-and-enabling-log-collection"></a>Configuration et activation de la collecte de journaux
Le pipeline EventFlow, chargé d’envoyer les journaux, est créé à partir d’une spécification stockée dans un fichier de configuration. Le package `Microsoft.Diagnostics.EventFlow.ServiceFabric` installe un fichier de configuration EventFlow de démarrage dans le dossier `PackageRoot\Config` de la solution. Le nom de ce fichier est `eventFlowConfig.json`. Ce fichier de configuration doit être modifié pour capturer les données à partir de la classe `EventSource` du service par défaut et envoyer les données au service Application Insights.

> [!NOTE]
> Nous partons du principe que vous êtes familiarisé avec le service **Azure Application Insights** et que vous disposez d’une ressource Application Insights que vous prévoyez d’utiliser pour surveiller votre service Service Fabric. Si vous avez besoin d’informations supplémentaires, consultez [Création d’une ressource Application Insights dans Azure](../application-insights/app-insights-create-new-resource.md).

Ouvrez le fichier `eventFlowConfig.json` dans l’éditeur et modifiez son contenu comme indiqué ci-dessous. Veillez à remplacer le nom de l’élément ServiceEventSource et la clé d’instrumentation Application Insights conformément aux commentaires. 

```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

> [!NOTE]
> Le nom de l’élément ServiceEventSource du service correspond à la valeur de la propriété Name de l’élément `EventSourceAttribute` appliqué à la classe ServiceEventSource. Tout cela est spécifié dans le fichier `ServiceEventSource.cs`, qui est inclus dans le code du service. Par exemple, dans l’extrait de code suivant, le nom de l’élément ServiceEventSource est *MyCompany-Application1-Stateless1* :
> ```csharp
> [EventSource(Name = "MyCompany-Application1-Stateless1")]
> internal sealed class ServiceEventSource : EventSource
> {
>    // (rest of ServiceEventSource implementation)
>} 
> ```

Notez que le fichier `eventFlowConfig.json` fait partie du package de configuration du service. Les modifications apportées à ce fichier peuvent être incluses dans les mises à niveau portant sur l’intégralité du service ou sur sa configuration uniquement, qui sont soumises aux contrôles d’intégrité et à la restauration automatique de Service Fabric en cas d’échec de la mise à niveau. Pour plus d’informations, consultez [Mise à niveau des applications Service Fabric](service-fabric-application-upgrade.md).

L’étape finale consiste à instancier le pipeline EventFlow dans le code de démarrage de votre service, qui se trouve dans le fichier `Program.cs`. Dans l’exemple suivant, les ajouts liés à EventFlow sont signalés par des commentaires introduits par `****` :

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    
                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

Le nom transmis comme paramètre de la méthode `CreatePipeline` de l’élément `ServiceFabricDiagnosticsPipelineFactory` correspond au nom de l’*entité d’intégrité* représentant le pipeline de collection des journaux EventFlow. Ce nom est utilisé si EventFlow rencontre une erreur et la signale via le sous-système d’intégrité de Service Fabric.

## <a name="verification"></a>Vérification
Démarrez votre service et observez la fenêtre de sortie Débogage de Visual Studio. Une fois le service démarré, vous devriez commencer à voir des signes qu’il envoie des enregistrements « Application Insights Telemetry ». Ouvrez un navigateur web et accédez à votre ressource Application Insights. Ouvrez l’onglet Recherche (en haut du panneau Vue d’ensemble par défaut). Après un court instant, vous devriez commencer à voir vos traces dans le portail Application Insights :

![Portail Application Insights montrant les journaux issus d’une application Service Fabric][2]

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur le diagnostic et la surveillance d’un service Fabric Service](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
* [Documentation relative à EventFlow](https://github.com/Azure/diagnostics-eventflow)


<!--Image references-->
[1]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/eventflow-nugets.png
[2]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/ai-traces.png


<!--HONumber=Jan17_HO3-->


