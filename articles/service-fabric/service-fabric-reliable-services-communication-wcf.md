<properties
   pageTitle="Pile de communication WCF Reliable Services | Microsoft Azure"
   description="La pile de communication WCF intégrée dans Service Fabric fournit une communication WCF client-service pour Reliable Services."
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
   ms.date="11/17/2015"
   ms.author="bharatn@microsoft.com"/>

# Pile de communication WCF pour Reliable Services
L'infrastructure Reliable Services permet aux auteurs de service de choisir la pile de communication qu'ils souhaitent utiliser pour leur service. Ils peuvent intégrer la pile de communication de leur choix par le biais du **ICommunicationListener** retourné à partir des méthodes [CreateServiceReplicaListeners ou CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md). L'infrastructure fournit une implémentation de la pile de communication basée sur Windows Communication Foundation (WCF) pour les auteurs de service qui souhaitent utiliser une communication WCF.

## Écouteur de communication WCF
L'implémentation spécifique à WCF de **ICommunicationListener** est fournie par la classe **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener**.

Supposons que nous disposions d’un contrat de service de type `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Nous pouvons créer un écouteur de communication WCF dans le service de la manière suivante.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## Écriture de clients pour la pile de communication WCF
Pour l’écriture de clients communiquant avec les services à l’aide de la méthode WCF, l’infrastructure fournit **WcfClientCommunicationFactory**, qui est l’implémentation propre à WCF de [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Le canal de communication WCF est accessible à partir de **WcfCommunicationClient** créé par **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Le code client peut utiliser **WcfCommunicationClientFactory**, avec **WcfCommunicationClient** qui implémente **ServicePartitionClient** pour déterminer le point de terminaison du service et communiquer avec le service.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
>[AZURE.NOTE] L’élément ServicePartitionResolver par défaut suppose que le client s’exécute dans le même cluster que le service. Dans le cas contraire, créez un objet ServicePartitionResolver et transmettez les points de terminaison de connexion du cluster.

## Étapes suivantes
* [Appel de procédure distante avec Reliable Services à distance](service-fabric-reliable-services-communication-remoting.md)

* [API Web avec OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Sécurisation des communications pour Reliable Services](service-fabric-reliable-services-secure-communication.md)

<!---HONumber=AcomDC_0330_2016-->