<properties
   pageTitle="Communication à distance des services dans Service Fabric | Microsoft Azure"
   description="La communication à distance dans Service Fabric permet aux clients et aux services de communiquer avec les services en utilisant un appel de procédure distante."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/12/2015"
   ms.author="bharatn@microsoft.com"/>

# Communication à distance des services avec Reliable Services
Pour les services qui ne sont pas liés à une pile ou un protocole de communication particulier, comme WebAPI, Windows Communication Foundation (WCF) ou autres, l'infrastructure fournit un mécanisme de communication à distance pour configurer rapidement et facilement un appel de procédure distante pour les services.

## Configuration de la communication à distance sur un service
La configuration de communication à distance pour un service s'effectue en deux étapes simples :

1. Créez une interface pour implémenter votre service. Cette interface définit les méthodes disponibles pour l'appel de procédure distante sur votre service. Ces méthodes doivent être des méthodes asynchrones retournant des tâches. L'interface doit implémenter `Microsoft.ServiceFabric.Services.Remoting.IService` pour signaler que le service dispose d'une interface de communication à distance.
2. Utilisez `FabricTransportServiceRemotingListener` dans votre service. Il s'agit d'une implémentation `ICommunicationListener` qui fournit des fonctionnalités de communication à distance.

Par exemple, ce service Hello World expose une méthode pour obtenir « Hello World » sur un appel de procédure distante :

```csharp
public interface IHelloWorldStateful : IService
{
    Task<string> GetHelloWorld();
}

internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]{
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this))};
    }

    public Task<string> GetHelloWorld()
    {
        return Task.FromResult("Hello World!");
    }
}

```
> [AZURE.NOTE] Les arguments et les types de retour dans l'interface de service peuvent être de type simple, complexe ou personnalisé, mais ils doivent être sérialisables par l'élément [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) .NET.


## Méthodes d’appel de service distant
Les méthodes d'appel sur un service à l'aide de la pile de communication à distance utilisent un proxy local pour le service via la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`. La méthode `ServiceProxy` crée un proxy local à l'aide de l'interface implémentée par le service. Avec ce proxy, vous pouvez appeler simplement et à distance des méthodes sur l'interface.


```csharp

IHelloWorldStateful helloWorldClient = ServiceProxy.Create<IHelloWorldStateful>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.GetHelloWorld();

```

L'infrastructure de communication à distance propage les exceptions levées au niveau du service au client. Par conséquent, la logique de gestion des exceptions au niveau du client à l'aide de `ServiceProxy` peut directement traiter les exceptions levées par le service.

## Étapes suivantes

* [API Web avec OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Communication WCF avec Reliable Services](service-fabric-reliable-services-communication-wcf.md)

* [Sécurisation des communications pour Reliable Services](service-fabric-reliable-services-secure-communication.md)

<!---HONumber=AcomDC_0330_2016-->