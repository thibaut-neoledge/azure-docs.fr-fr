<properties
   pageTitle="Communication à distance des services dans Service Fabric | Microsoft Azure"
   description="La communication à distance dans Service Fabric permet aux clients et aux services de communiquer avec les services en utilisant un appel de procédure distante."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# Communication à distance des services avec Reliable Services
Pour les services qui ne sont pas liés à une pile ou un protocole de communication particulier, comme WebAPI, Windows Communication Foundation (WCF) ou autres, l’infrastructure Reliable Services fournit un mécanisme de communication à distance pour configurer rapidement et facilement un appel de procédure distante pour les services.

## Configuration de la communication à distance sur un service
La configuration de communication à distance pour un service s'effectue en deux étapes simples :

1. Créez une interface pour implémenter votre service. Cette interface définit les méthodes disponibles pour l'appel de procédure distante sur votre service. Ces méthodes doivent être des méthodes asynchrones retournant des tâches. L'interface doit implémenter `Microsoft.ServiceFabric.Services.Remoting.IService` pour signaler que le service dispose d'une interface de communication à distance.
2. Utilisez un écouteur de communication à distance dans votre service. Il s'agit d'une implémentation `ICommunicationListener` qui fournit des fonctionnalités de communication à distance. L’espace de noms `Microsoft.ServiceFabric.Services.Remoting.Runtime` contient une méthode d’extension `CreateServiceRemotingListener` pour les services avec et sans état qui peuvent être utilisés pour créer un écouteur de communication à distance à l’aide du protocole de transport de communication à distance par défaut.

Par exemple, le service sans état suivant expose une méthode unique pour obtenir « Hello World » sur un appel de procédure distante :

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> GetHelloWorld();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorld()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context => 
            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [AZURE.NOTE] Les arguments et les types de retour dans l’interface de service peuvent être de type simple, complexe ou personnalisé, mais ils doivent être sérialisables par l’élément [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) .NET.


## Méthodes d’appel de service distant
L’appel de méthodes sur un service à l’aide de la pile de communication à distance est effectué avec un proxy local pour le service via la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`. La méthode `ServiceProxy` crée un proxy local à l’aide de l’interface implémentée par le service. Avec ce proxy, vous pouvez appeler simplement et à distance des méthodes sur l'interface.


```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.GetHelloWorld();

```

L'infrastructure de communication à distance propage les exceptions levées au niveau du service au client. Par conséquent, la logique de gestion des exceptions au niveau du client à l’aide de `ServiceProxy` peut directement traiter les exceptions levées par le service.

## Étapes suivantes

* [API Web avec OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Communication WCF avec Reliable Services](service-fabric-reliable-services-communication-wcf.md)

* [Sécurisation des communications pour Reliable Services](service-fabric-reliable-services-secure-communication.md)

<!---HONumber=AcomDC_0713_2016-->