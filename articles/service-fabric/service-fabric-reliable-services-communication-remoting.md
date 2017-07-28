
---
title: "Communication à distance des services dans Service Fabric | Microsoft Docs"
description: "La communication à distance dans Service Fabric permet aux clients et aux services de communiquer avec les services en utilisant un appel de procédure distante."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 92a8894f24c234fbf38eda086531b524cceccfc1
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017


---
# <a name="service-remoting-with-reliable-services"></a>Communication à distance des services avec Reliable Services
Pour les services qui ne sont pas liés à une pile ou un protocole de communication particulier, comme WebAPI, Windows Communication Foundation (WCF) ou autres, l’infrastructure Reliable Services fournit un mécanisme de communication à distance pour configurer rapidement et facilement un appel de procédure distante pour les services.

## <a name="set-up-remoting-on-a-service"></a>Configuration de la communication à distance sur un service
La configuration de communication à distance pour un service s'effectue en deux étapes simples :

1. Créez une interface pour implémenter votre service. Cette interface définit les méthodes disponibles pour l'appel de procédure distante sur votre service. Ces méthodes doivent être des méthodes asynchrones retournant des tâches. L'interface doit implémenter `Microsoft.ServiceFabric.Services.Remoting.IService` pour signaler que le service dispose d'une interface de communication à distance.
2. Utilisez un écouteur de communication à distance dans votre service. Il s'agit d'une implémentation `ICommunicationListener` qui fournit des fonctionnalités de communication à distance. L’espace de noms `Microsoft.ServiceFabric.Services.Remoting.Runtime` contient une méthode d’extension `CreateServiceRemotingListener` pour les services avec et sans état qui peuvent être utilisés pour créer un écouteur de communication à distance à l’aide du protocole de transport de communication à distance par défaut.

Remarque : L’espace de noms `Remoting` est disponible sous la forme d’un package NuGet distinct appelé `Microsoft.ServiceFabric.Services.Remoting` 

Par exemple, le service sans état suivant expose une méthode unique pour obtenir « Hello World » sur un appel de procédure distante :

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context =>            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Les arguments et les types de retour dans l’interface de service peuvent être de type simple, complexe ou personnalisé, mais ils doivent être sérialisables par l’élément [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).NET.
>
>

## <a name="call-remote-service-methods"></a>Méthodes d’appel de service distant
L’appel de méthodes sur un service à l’aide de la pile de communication à distance est effectué avec un proxy local pour le service via la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . La méthode `ServiceProxy` crée un proxy local à l’aide de l’interface implémentée par le service. Avec ce proxy, vous pouvez appeler simplement et à distance des méthodes sur l'interface.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

L'infrastructure de communication à distance propage les exceptions levées au niveau du service au client. Par conséquent, la logique de gestion des exceptions au niveau du client à l’aide de `ServiceProxy` peut directement traiter les exceptions levées par le service.

## <a name="service-proxy-lifetime"></a>Durée de vie du proxy du service
Comme la création de proxy de service est une opération légère, l’utilisateur peut en créer autant de fois que nécessaire. Le proxy de service peut être réutilisé tant que l’utilisateur en a besoin. L’utilisateur peut réutiliser le même proxy en cas d’exception. Chaque proxy de service contient un client de communication qui permet d’envoyer des messages sur le réseau. Lors de l’appel de l’API, un contrôle interne vérifie si le client de communication utilisé est valide. En fonction de ce résultat, nous recréons le client de communication. Par conséquent, l’utilisateur n’a pas besoin de recréer le proxy de service en cas d’exception.

### <a name="serviceproxyfactory-lifetime"></a>Durée de vie de la fabrique ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) est une fabrique qui crée le proxy pour différentes interfaces de communication à distance. Si vous utilisez l’API ServiceProxy.Create pour la création de proxy, l’infrastructure crée le ServiceProxyFactory singleton.
Il est utile d’en créer un manuellement lorsque vous devez remplacer les propriétés [IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory).
La fabrique est une opération coûteuse. ServiceProxyFactory conserve le cache du client de communication.
Il est recommandé de mettre en cache ServiceProxyFactory aussi longtemps que possible.

## <a name="remoting-exception-handling"></a>Gestion des exceptions à distance
Toutes les exceptions à distance levées par l’API de service sont envoyées au client en tant que AggregateException. Les exceptions à distance doivent être des contrats de données sérialisables. Sinon, une exception [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) est envoyée à l’API de proxy en indiquant l’erreur de sérialisation.

Le proxy de service gère toutes les exceptions de basculement pour la partition de service pour laquelle il a été créé. Il résout à nouveau les points de terminaison s’il existe des exceptions de basculement (non temporaires) et retente l’appel avec le point de terminaison correct. Le nombre de tentatives pour l’exception de basculement est illimité.
En cas d’exceptions temporaires, il retente uniquement l’appel.

Les paramètres de nouvelle tentative par défaut sont fournis par [OperationRetrySettings]. (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) L’utilisateur peut configurer ces valeurs en transmettant l’objet OperationRetrySettings au constructeur ServiceProxyFactory.

## <a name="next-steps"></a>Étapes suivantes
* [API Web avec OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Communication WCF avec Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Sécurisation des communications pour Reliable Services](service-fabric-reliable-services-secure-communication.md)

