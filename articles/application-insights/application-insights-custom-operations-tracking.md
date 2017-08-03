---
title: "Suivi des opérations personnalisées avec le kit SDK .NET d’Azure Application Insights | Microsoft Docs"
description: "Suivi des opérations personnalisées avec le kit SDK .NET d’Azure Application Insights"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/31/2017
ms.author: sergkanz
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 883750c939aa3bd605189f513e5ce74642f91709
ms.contentlocale: fr-fr
ms.lasthandoff: 07/13/2017

---

# <a name="tracking-custom-operations-with-application-insights-net-sdk"></a>Suivi des opérations personnalisées avec le kit SDK .NET d’Application Insights

Les kits SDK d’Application Insights effectuent automatiquement le suivi des appels et demandes HTTP entrants à des services dépendants - des requêtes HTTP, des requêtes SQL, etc. Considérons une application composée de plusieurs micro-services. Le suivi et la corrélation des demandes et des dépendances vous offrent une meilleure visibilité de la réactivité et de la fiabilité de l’application entière sur l’ensemble des micro-services. Nous allons progressivement développer la liste et fournir une collection automatique des autres plateformes et infrastructures bien connues. 

Il existe une classe de modèles d’application qui ne peuvent pas être pris en charge de façon générique. La surveillance appropriée de ces modèles requiert une instrumentation manuelle du code. Cet article traite de quelques modèles pouvant nécessiter une instrumentation manuelle. Notamment une file d’attente personnalisée qui traite ou exécute une tâche à long terme en arrière-plan.

Ce document fournit des conseils sur la façon d’effectuer le suivi d’opérations personnalisées avec le kit SDK Application Insights.

Ce document s’applique à :
- Application Insights pour .NET (également appelé Kit SDK de base) version `2.4+`
- Application Insights pour applications web (exécute ASP.NET) version `2.4+`
- Application Insights pour AspNetCore version `2.1+`

## <a name="overview"></a>Vue d'ensemble
Par opération, nous faisons référence à un travail logique exécuté par l’application. Il a un nom, une heure de début, une durée, un contexte d’exécution tel qu’un nom d’utilisateur, des propriétés et un résultat. Si l’opération `A` a été initiée par l’opération `B`, l’opération `B` est définie comme parent de l’opération `A`.  Une opération ne peut avoir qu’un seul parent et de nombreuses opérations enfants. Vous trouverez plus d’informations sur les opérations et la corrélation de télémétrie [ici](application-insights-correlation.md).

Dans le kit SDK .NET d’Application Insights, une opération est décrite par la classe abstraite [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/Extensibility/Implementation/OperationTelemetry.cs) et par ses descendants [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/RequestTelemetry.cs) et [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Suivi des opérations entrantes 
Le kit SDK web d’Application Insights collecte automatiquement des requêtes HTTP pour les applications ASP.NET qui s’exécutent dans le pipeline IIS et toutes les applications ASP.NET Core. Il existe des solutions prises en charge par la communauté pour les autres plateformes et infrastructures. Toutefois, si l’application n’est prise en charge par aucune solution standard ni prise en charge par la communauté, vous pouvez l’instrumenter manuellement.

Un autre exemple nécessitant un suivi personnalisé est le rôle de travail qui reçoit des éléments de la file d’attente. Pour certaines files d’attente, l’appel visant à ajouter un message à cette file d’attente est comptabilisé en tant que dépendance. Toutefois, l’opération de haut niveau qui décrit le traitement des messages n’est pas collectée automatiquement.

Voyons comment effectuer le suivi de telles opérations.

À un niveau élevé, la tâche consiste à créer `RequestTelemetry` et à définir des propriétés connues. Une fois l’opération terminée, effectuez le suivi de la télémétrie. L’exemple suivant illustre cela.

### <a name="http-request-in-owin-self-hosted-app"></a>Requête HTTP dans une application Owin auto-hébergée
Nous suivons le [protocole http pour la corrélation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) et vous devez vous attendre à recevoir les en-têtes décrits ici.

``` C#
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // if there is Request-Id recevied from the upstream service, set telemetry context accordingly
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows to correlate 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry item
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // process request
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // update status code and success as appropriate
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // now it's time to stop operation (and track telemetry)
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // returns root Id from the '|' to first '.' if any
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Le protocole de corrélation HTTP déclare également l’en-tête `Correlation-Context`. Toutefois, il a été omis ici dans un souci de simplicité.

## <a name="queue-instrumentation"></a>Instrumentation des files d’attente
Pour la communication HTTP, nous avons créé un protocole pour transmettre les détails de corrélation. Certains protocoles de files d’attente vous permettent de transmettre des métadonnées supplémentaires avec le message et d’autres ne le permettent pas.

### <a name="service-bus-queue"></a>File d’attente Service Bus
La [file d’attente ServiceBus](../service-bus-messaging/index.md) vous permet de transmettre un conteneur des propriétés avec le message et nous l’utilisons pour transmettre l’ID de corrélation.

La file d’attente ServiceBus utilise des protocoles TCP. Application Insights n’effectue pas automatiquement le suivi des opérations de file d’attente, et nous devons les suivre manuellement. L’opération de retrait de file d’attente est une API de style Push et nous ne sommes pas en mesure d’effectuer son suivi intégral.

#### <a name="enqueue"></a>Enqueue (empiler)

```C#
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes telemetry item
    // and allows to correlate this operation with its parent and children
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // ServiceBus Queue allows to pass property bag along with the message
    // we will use them to pass our correlation identifiers (and other context)
    // to the consumer
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```C#
public async Task Process(BrokeredMessage message)
{
    // once the message is taken from the queue, create ReqeustTelemetry to track its processing
    // it may also make sense to get name from the message
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>File d’attente de stockage Azure
L’exemple suivant montre comment effectuer le suivi des opérations de [file d’attente de stockage Azure](../storage/storage-dotnet-how-to-use-queues.md) et mettre en corrélation la télémétrie entre le producteur, le consommateur et le stockage Azure. 

La file d’attente de stockage Azure a une API HTTP et tous les appels à la file d’attente sont suivis par ApplicationInsights DependencyCollector pour les requêtes HTTP.
Assurez-vous d’avoir `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` dans `applicationInsights.config`, ou ajoutez-le par programmation, comme décrit [ici](app-insights-api-filtering-sampling.md).

Si vous configurez manuellement ApplicationInsights, assurez-vous de créer et d’initialiser `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` de façon semblable au code suivant :
 
``` C#
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// you can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add Azure Storage endpoint, otherwise you may experience request signature validation issues on the Storage service side
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// do not forget to dispose module during application shutdown
```

Vous pouvez également mettre en corrélation l’ID d’opération ApplicationInsights avec l’ID de requête (RequestId) du stockage Azure. Consultez [cet article](../storage/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing) sur la façon de définir et d’obtenir un client de demande de stockage et l’ID de la demande serveur.

#### <a name="enqueue"></a>Enqueue (empiler)
Étant donné que les files d’attente de stockage Azure prennent en charge l’API HTTP, toutes les opérations mettant en jeu la file d’attente sont automatiquement suivies par ApplicationInsights. Dans de nombreux cas, cette instrumentation doit être suffisante.
Toutefois, pour mettre en corrélation les traces côté client avec les traces du producteur, vous devez transmettre un contexte de corrélation de façon similaire à la manière utilisée dans « Protocole http pour la corrélation ». 

Dans l’exemple ci-dessous, nous effectuons le suivi de l’opération `Enqueue` facultative. Cela permet de
 - Mettre en corrélation les nouvelles tentatives (le cas échéant) : elles ont toutes un parent commun qui est l’opération `Enqueue`. Dans le cas contraire, elles sont comptabilisées en tant qu’enfants de la demande entrante. Ainsi, s’il existe plusieurs demandes logiques pour la file d’attente, il pourrait être difficile de trouver quel appel a généré de nouvelles tentatives.
 - Mettre en corrélation les journaux de stockage Azure (si nécessaire) avec la télémétrie ApplicationInsights.

L’opération `Enqueue` est l’enfant d’une opération « parent » (par exemple, requête HTTP entrante) et l’appel de dépendance « Http » est l’enfant de l’opération `Enqueue` et le petit-enfant de la demande entrante.

```C#
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload
    // e.g. if you choose to pass payload serialized to json, it may look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Azure Storage logs and ApplciationInsights telemetry
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}  
```

Si vous souhaitez réduire la quantité de données de télémétrie que votre application signale ou si vous ne souhaitez pas suivre l’opération `enqueue` pour d’autres raisons, vous pouvez utiliser directement l’API `Activity` :

- Créez (et démarrez) une nouvelle `Activity` au lieu de démarrer l’opération ApplicationInsights (vous N’avez PAS besoin d’assigner de propriétés sur celle-ci, à l’exception du nom de l’opération).
- Sérialisez `yourActivity.Id` dans la charge utile du message à la place de `operation.Telemetry.Id`. Vous pouvez également utiliser `Activity.Current.Id`.


#### <a name="dequeue"></a>Dequeue (Enlever de la file d’attente)
Comme avec `Enqueue`, la requête HTTP réelle à la file d’attente de stockage Azure est automatiquement suivie par ApplicationInsights. Toutefois, l’opération `Enqueue` se produit vraisemblablement dans le contexte parent tel que le contexte de demande « entrant ». Les kits SDK d’Application Insights mettent automatiquement en corrélation cette opération (et sa partie HTTP) avec la demande parent et d’autres données de télémétrie signalées dans le même cadre.

L’opération `Dequeue` est compliquée : le kit SDK d’Application Insights effectue automatiquement le suivi des requêtes HTTP, mais ne connaît pas le contexte de corrélation tant que le message n’est pas analysé. Il n’est pas possible de mettre en corrélation la requête HTTP afin d’obtenir le message avec le reste des données de télémétrie.

Dans de nombreux cas, il peut être utile de mettre en corrélation la requête HTTP adressée à la file d’attente avec d’autres traces également. L’exemple suivant illustre la façon d’y parvenir.

``` C#
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // if there is a message, we want to correlate Dequeue operation with processing
            // However we will only know what correlation Id to use AFTER we will get it from the message
            // So we will report telemetry once we know the Ids.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // delete message
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Process

Dans l’exemple suivant, nous traçons le message « entrant » de la même façon que la requête HTTP entrante.

```C#
public async Task Process(MessagePayload message)
{
    // once the message is dequeued from the queue, create ReqeustTelemetry to track it's processing
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // it may also make sense to get name from the message
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

De même, les autres opérations de file d’attente peuvent être instrumentées. Une opération d’aperçu doit être instrumentée de façon similaire à un retrait de la file d’attente. L’instrumentation des opérations de gestion de file d’attente n’est pas nécessaire. Application Insights effectue le suivi d’opérations telles que HTTP et, dans la plupart des cas, cela suffit.

Lors de l’instrumentation d’une suppression de message, assurez-vous de définir les identificateurs de l’opération (corrélation). Vous pouvez également utiliser l’API `Activity` (vous n’avez alors pas besoin de définir des identificateurs d’opérations sur les éléments de télémétrie, ApplicationInsights le fait pour vous) :

- Créez une nouvelle API `Activity` une fois que vous avez un élément à partir de la file d’attente.
- Utilisez `Activity.SetParentId(message.ParentId)` pour mettre en corrélation les journaux du consommateur et du producteur.
- Démarrez l’API `Activity`.
- Effectuez le suivi des opérations Dequeue, Process et Delete à l’aide des programmes d’assistance `Start/StopOperation`. Procédez à partir du même flux de contrôle asynchrone (contexte d’exécution). De cette manière, elles sont correctement mises en corrélation.
- Ensuite, arrêtez l’API `Activity`.
- Utilisez `Start/StopOperation` ou appelez manuellement la télémétrie Track. 

### <a name="batch-processing"></a>Traitement par lots
Certaines files d’attente permettent de retirer de la file d’attente plusieurs messages avec une seule demande, mais le traitement de ces messages est supposé indépendant et appartient aux différentes opérations logiques.
Dans ce cas, il n’est pas possible de mettre en corrélation l’opération `Dequeue` avec un traitement de message particulier.

Chaque traitement de message doit se produire dans son propre flux de contrôle asynchrone. Vous trouverez plus de détails sur cela dans la section [Suivi des dépendances sortantes](#outgoing-dependencies-tracking).

## <a name="long-running-background-tasks"></a>Tâches en arrière-plan à long terme
Certaines applications démarrent une opération à long terme qui peut être due à des demandes de l’utilisateur. Du point de vue du traçage/de l’instrumentation, ce n’est pas différent de l’instrumentation des demandes ou des dépendances. 

``` C#
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // process task
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // update status code and success as appropriate
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // update status code and success as appropriate
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

Dans cet exemple, nous utilisons `telemetryClient.StartOperation` pour créer `RequestTelemetry` et remplir le contexte de corrélation. Supposons que vous avez une opération parent, par exemple, qui a été créée par les demandes entrantes qui ont planifié l’opération. Tant que `BackgroundTask` démarre dans le même flux de contrôle asynchrone en tant que demande entrante, elle est mise en corrélation avec cette opération parent. `BackgroundTask` et tous les éléments de télémétrie imbriqués sont automatiquement mis en corrélation avec la demande à son origine, même après la fin de la demande.

Lorsque la tâche est démarrée à partir du thread en arrière-plan qui n’a pas d’opération (Activity) associée, `BackgroundTask` n’a pas de parent. Toutefois, elle peut avoir des opérations imbriquées et tous les éléments de télémétrie signalés à partir de la tâche sont mis en corrélation avec l’élément `RequestTelemetry` créé dans l’élément `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Suivi des dépendances sortantes
Voulez-vous effectuer le suivi de votre propre genre de « dépendance » ou d’une opération qui n’est pas prise en charge par ApplicationInsights.

La méthode `Enqueue` dans la file d’attente ServiceBus ou la file d’attente de stockage Azure peut servir d’exemples pour ce type de suivi personnalisé.

L’approche générale utilisée pour le suivi personnalisé des dépendances est la suivante :
- Appelez la méthode `TelemetryClient.StartOperation` (extension) qui remplit les propriétés `DependencyTelemetry` nécessaires pour la corrélation et d’autres propriétés (heure de début, durée).
- Définissez d’autres propriétés personnalisées sur l’élément `DependencyTelemetry` : le nom et tout autre contexte dont vous avez besoin.
- Effectuez l’appel à la dépendance et attendez-le.
- Arrêtez l’opération avec `StopOperation` lorsqu’elle est terminée.
- Traitez les exceptions.

`StopOperation` arrête uniquement l’opération qui a été démarrée : si l’opération en cours d’exécution ne correspond pas à celle que vous souhaitez arrêter, StopOperation n’a aucun effet. Cela peut se produire si vous démarrez plusieurs opérations en parallèle dans le même contexte d’exécution :

```C#
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// this will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Ainsi, vous devez veiller à toujours appeler `StartOperation` et exécuter votre tâche dans son propre contexte :
```C#
public async Task RunMyTaskAsync()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
    try 
    {
        var myTask = await StartMyTaskAsync();
        // update status code and success as appropriate
    }
    catch(...) 
    {
        // update status code and success as appropriate
    }
    finally 
    {
        telemetryClient.StopOperation(operation);
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les bases de la [corrélation de télémétrie](application-insights-correlation.md) dans Application Insights.
- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](application-insights-data-model.md).
- Signalez les [événements et métriques](app-insights-api-custom-events-metrics.md) à Application Insights .
- Découvrez la [configuration](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) de la collection de propriétés de contexte standard.
- Consultez le [Guide de l’utilisateur System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) pour voir comment mettre en corrélation les données de télémétrie.

