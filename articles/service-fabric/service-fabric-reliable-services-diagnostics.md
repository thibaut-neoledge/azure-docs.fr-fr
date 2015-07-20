<properties
   pageTitle="Diagnostics de service fiable avec état"
   description="Fonctionnalité de diagnostic pour services fiables avec état"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/20/2015"
   ms.author="alanwar"/>

# Fonctionnalité de diagnostic pour services fiables avec état
La classe StatefulServiceBase de services fiables avec état émet des événements [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) qui peuvent être utilisés pour déboguer le service, fournir des informations sur le fonctionnement du runtime et vous aider lors du dépannage.

## Événements EventSource
Le nom EventSource de la classe StatefulServiceBase de services fiables avec état est « Microsoft-ServiceFabric-Services ». Les événements issus de cette source d'événements s'affichent dans la fenêtre [Événements de diagnostics](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) lorsque le service est en cours de [débogage dans Visual Studio](service-fabric-debugging-your-application.md).

Service Fabric offre également la possibilité de diriger ces événements vers [Application Insights](http://azure.microsoft.com/services/application-insights/). Pour plus d'informations, consultez l'article sur la [configuration d'Application Insights pour Service Fabric](service-fabric-diagnostics-application-insights-setup.md).

[PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnostics Microsoft Azure](../cloud-services-dotnet-diagnostics.md) et [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent) constituent d'autres exemples d'outils et de technologies permettant de collecter et/ou d'afficher des événements EventSource.

## Événements

|Nom de l'événement|ID de l'événement|Level|Description de l'événement|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|Informations|Émis lorsque la tâche de service RunAsync est démarrée|
|StatefulRunAsyncCancellation|2|Informations|Émis lorsque la tâche de service RunAsync est annulée|
|StatefulRunAsyncCompletion|3|Informations|Émis lorsque la tâche de service RunAsync est terminée|
|StatefulRunAsyncSlowCancellation|4|Avertissement|Émis lorsque la tâche de service RunAsync prend trop de temps pour terminer l'annulation|
|StatefulRunAsyncFailure|5|Erreur|Émis lorsque la tâche de service RunAsync renvoie une exception|

## Interprétation des événements

Les événements StatefulRunAsyncInvocation, StatefulRunAsyncCompletion et StatefulRunAsyncCancellation sont utiles à l'enregistreur du service pour comprendre le cycle de vie d'un service, ainsi que le minutage lorsqu'un service est démarré, annulé et terminé. Cela peut être utile lors du débogage de problèmes du service ou pour comprendre le cycle de vie du service.

Les enregistreurs de service doivent prêter attention aux événements StatefulRunAsyncSlowCancellation et StatefulRunAsyncFailure qui indiquent des problèmes liés au service.

StatefulRunAsyncFailure est émis à chaque fois que la tâche RunAsync() du service lève une exception. En général, une exception levée indique une erreur ou un bogue dans le service. En outre, l'exception provoque l'échec du service et par conséquent, son déplacement vers un autre nœud. Ceci peut constituer une opération coûteuse et retarder les requêtes entrantes pendant que le service est déplacé. Les enregistreurs de service doivent déterminer la cause de l'exception et, si possible, la résoudre.

StatefulRunAsyncSlowCancellation est émis à chaque fois qu'une requête d'annulation de la tâche RunAsync prend plus de 4 secondes. Lorsqu'un service prend trop de temps pour terminer l'annulation, cela a un impact sur la capacité du service à être redémarré rapidement sur un autre nœud. Cela peut avoir un impact sur la disponibilité globale du service.

<!---HONumber=July15_HO2-->