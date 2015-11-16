<properties
   pageTitle="Présentation de la communication Reliable Service | Microsoft Azure"
   description="Présentation du modèle de communication Reliable Service, notamment l'ouverture d'écouteurs sur les services, la résolution des points de terminaison et la communication entre les services."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="08/27/2015"
   ms.author="bharatn@microsoft.com"/>

# Présentation du modèle de communication Reliable Service

Le modèle de programmation de services fiables permet aux auteurs de services de spécifier le mécanisme de communication qu'ils souhaitent utiliser pour présenter leurs points de terminaison de service et il fournit des abstractions que les clients peuvent utiliser pour détecter et communiquer avec le point de terminaison de service.

## Configuration de la pile de communication du service

L'API de services fiables permet aux auteurs de services d'intégrer la pile de communication de leur choix dans le service en implémentant la méthode suivante dans leur service.

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

L'interface `ICommunicationListener` définit l'interface qui doit être implémentée par l'écouteur de communication pour un service.

```csharp

public interface ICommunicationListener
{
    void Initialize(ServiceInitializationParameters serviceInitializationParameters);

    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```
Les points de terminaison qui sont requis pour le service sont décrits via le [manifeste de service](service-fabric-application-model.md), dans la section consacrée aux points de terminaison.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

L'écouteur de communication peut accéder aux ressources du système d'extrémité allouées à partir du `CodePackageActivationContext` dans les `ServiceInitializationParameters`, et commencer à écouter les demandes lorsqu'il est ouvert.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]Les ressources du point de terminaison sont communes au package de service entier et sont allouées par Service Fabric lorsque le package de service est activé. Par conséquent, tous les réplicas hébergés dans le même service ServiceHost partagent le même port. Cela signifie que l'écouteur de communication doit prendre en charge le partage de port. La méthode recommandée consiste à configurer l'écouteur de communication afin qu'il utiliser l'ID de la partition et l'ID du réplica/de l'instance lors de la génération de l'adresse d'écoute.

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
   replicaOrInstanceId = parameters.InstanceId;
}
else
{
   replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

## Communication du client au service
L'API de services fiables fournit les abstractions suivantes qui facilitent l'écriture des clients pour la communication avec les services.

## ServicePartitionResolver
La classe ServicePartitionResolver permet au client d'identifier le système d'extrémité d'un service Service Fabric lors de l'exécution.

> [AZURE.TIP]Le processus permettant de déterminer le point de terminaison d'un service est appelé résolution de point de terminaison dans la terminologie Service Fabric.

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
   long partitionKey,
   CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
   CancellationToken cancellationToken);


```
> [AZURE.NOTE]FabricClient est l'objet utilisé pour communiquer avec le cluster Service Fabric pour diverses opérations de gestion sur le cluster Service Fabric.

Généralement, le code client ne doit pas fonctionner directement avec `ServicePartitionResolver`. Il est créé et transmis à d'autres classes d'assistance dans l'API de services fiables, qui utilisent le résolveur interne et aident le client à communiquer avec le service.

## CommunicationClientFactory
`ICommunicationClientFactory` définit l'interface de base implémentée par une usine de clients de communication qui produit des clients capables de communiquer avec un service Service Fabric. L'implémentation de CommunicationClientFactory dépend de la pile de communication utilisée par le service Service Fabric avec lequel le client souhaite communiquer. L'API de services fiables fournit une classe CommunicationClientFactoryBase<TCommunicationClient> qui offre une implémentation de base de cette interface `ICommunicationClientFactory` et effectue des tâches communes à toutes les piles de communication (semblable à l'utilisation d'un `ServicePartitionResolver` pour déterminer le point de terminaison du service). Les clients implémentent généralement la classe abstraite CommunicationClientFactoryBase pour gérer la logique spécifique de la pile de communication.

```csharp

protected CommunicationClientFactoryBase(
   ServicePartitionResolver servicePartitionResolver = null,
   IEnumerable<IExceptionHandler> exceptionHandlers = null,
   IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
   public MyCommunicationClient(MyCommunicationChannel communicationChannel)
   {
      this.CommunicationChannel = communicationChannel;
   }
   public MyCommunicationChannel CommunicationChannel { get; private set; }
   public ResolvedServicePartition ResolvedServicePartition;
}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
   protected override void AbortClient(MyCommunicationClient1 client)
   {
      throw new NotImplementedException();
   }

   protected override Task<MyCommunicationClient> CreateClientAsync(ResolvedServiceEndpoint endpoint, CancellationToken cancellationToken)
   {
      throw new NotImplementedException();
   }

   protected override bool ValidateClient(MyCommunicationClient clientChannel)
   {
      throw new NotImplementedException();
   }

   protected override bool ValidateClient(ResolvedServiceEndpoint endpoint, MyCommunicationClient client)
   {
      throw new NotImplementedException();
   }
}

```

## ServicePartitionClient
`ServicePartitionClient` utilise le CommunicationClientFactory (et en interne le ServicePartitionResolver) et permet de communiquer avec le service en gérant les tentatives de communication courante et des exceptions temporaires de Fabric Service.

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

Un modèle d'utilisation typique ressemblerait à ceci :

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(
   client =>
   {
      // Communicate with the service using the client.
      throw new NotImplementedException();
   },
   CancellationToken.None);


... other client code ...

```

## Étapes suivantes
* [Pile de communication par défaut fournie par l'infrastructure de services fiables](service-fabric-reliable-services-communication-default.md)

* [Pile de communication WCF fournie par l'infrastructure de services fiables](service-fabric-reliable-services-communication-wcf.md)

* [Écriture d'un service à l'aide de l'API de services fiables qui utilise la pile de communication WebAPI](service-fabric-reliable-services-communication-webapi.md)
 

<!---HONumber=Nov15_HO2-->