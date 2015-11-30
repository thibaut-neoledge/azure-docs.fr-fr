<properties
   pageTitle="Pile de communication WCF Reliable Services | Microsoft Azure"
   description="La pile de communication WCF intégrée dans Services Fabric fournit une communication WCF client-service pour Reliable Services."
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

# Pile de communication WCF pour les services fiables
L'infrastructure de services fiables permet aux auteurs de service de choisir la pile de communication qu'ils souhaitent utiliser pour leur service. Ils peuvent intégrer la pile de communication de leur choix via le `ICommunicationListener` retourné à partir de la méthode [`CreateCommunicationListener`](../service-fabric-reliable-service-communication.md). L'infrastructure fournit une implémentation WCF de la pile de communication pour les auteurs de service qui souhaitent utiliser une communication WCF.

## Écouteur de communication WCF
L'implémentation WCF spécifique de `ICommunicationListener` est fournie par la classe `Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener`.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    // TODO: If your service needs to handle user requests, return a list of ServiceReplicaListeners here.
    return new[] { new ServiceReplicaListener(parameters =>
        new WcfCommunicationListener(typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // which identifies the endpoint that the wcf servicehost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        }
    )};
}

```

## Écriture de clients pour la pile de communication WCF
Pour écrire des clients afin qu'ils communiquent avec les services à l'aide de la méthode WCF, l'infrastructure fournit `WcfClientCommunicationFactory`, qui est l'implémentation WCF spécifique de [`ClientCommunicationFactoryBase`](../service-fabric-reliable-service-communication.md).

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

Le canal de communication WCF est accessible à partir du `WcfCommunicationClient` créé par le `WcfCommunicationClientFactory`

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

Le code client peut utiliser le `WcfCommunicationClientFactory` et le `ServicePartitionClient` pour déterminer le point de terminaison du service et communiquer avec le service.

```csharp

//
// Create a service resolver for resolving the endpoints of the calculator service.
//
ServicePartitionResolver serviceResolver = new ServicePartitionResolver(() => new FabricClient());

//
// Create the binding.
//
NetTcpBinding binding = CreateClientConnectionBinding();

var clientFactory = new WcfCommunicationClientFactory<ICalculator>(
    serviceResolver,// ServicePartitionResolver
    binding,        // Client binding
    null,           // Callback object
    null);          // donot retry Exception types


//
// Create a client for communicating with the calc service which has been created with
// Singleton partition scheme.
//
var calculatorServicePartitionClient = new ServicePartitionClient<WcfCommunicationClient<ICalculator>>(
    clientFactory,
    ServiceName);

//
// Call the service to perform the operation.
//
var result = calculatorServicePartitionClient.InvokeWithRetryAsync(
    client => client.Channel.AddAsync(2, 3)).Result;

```
 
## Étapes suivantes
* [Appel de procédure distante avec Reliable Services à distance](service-fabric-reliable-services-communication-remoting.md)

* [API Web avec OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=Nov15_HO4-->