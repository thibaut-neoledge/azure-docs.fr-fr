<properties
   pageTitle="Présentation de la communication Reliable Service | Microsoft Azure"
   description="Présentation du modèle de communication Reliable Service, notamment l'ouverture d'écouteurs sur les services, la résolution des points de terminaison et la communication entre les services."
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
   ms.date="11/17/2015"
   ms.author="vturecek@microsoft.com"/>

# Modèle de communication Reliable Service

Service Fabric, en tant que plateforme, est totalement indépendant de la communication entre les services. Toutes les piles et tous les protocoles sont possibles, de l’UDP au HTTP. C'est au développeur de services de choisir comment les services doivent communiquer. L'infrastructure d'application Reliable Services fournit quelques piles de communication prédéfinies ainsi que des outils pour déployer votre pile de communication personnalisée, comme des abstractions que les clients peuvent utiliser pour détecter des points de terminaison de service et communiquer avec.

## Configuration de la communication de service

L'API Reliable Services utilise une interface simple pour la communication de service. Afin d’ouvrir un point de terminaison pour votre service, il vous suffit d’implémenter l’interface suivante :

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Ajoutez ensuite votre implémentation d'écouteur de communication en la retournant dans une substitution de méthode de classe de base de services.

Sans état :

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

Avec état :

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

Dans les deux cas, vous retournez une collection d'écouteurs qui permet à votre service d'utiliser facilement plusieurs écouteurs ; par exemple, vous pouvez avoir un écouteur HTTP et un écouteur WebSocket distinct. Chaque écouteur obtient un nom, et la collection résultante du nom : paires d'adresses est représentée comme un objet JSON lorsqu'un client demande les adresses d'écoute pour une instance de service ou une partition.

Dans un service sans état, la substitution retourne une collection de ServiceInstanceListeners. Un ServiceInstanceListener contient simplement une fonction pour créer votre ICommunicationListener et lui attribuer un nom. Pour les services avec état, la substitution retourne une collection de ServiceReplicaListener. Cela diffère légèrement de son homologue sans état, car ServiceReplicaListener a une option pour ouvrir un ICommunicationListener sur des réplicas secondaires. Ainsi, non seulement vous pouvez utiliser plusieurs écouteurs de communication dans un service, mais vous pouvez également spécifier ceux qui acceptent les demandes sur les réplicas secondaires, et ceux qui écoutent uniquement sur les réplicas principaux.

Par exemple, nous pouvons avoir un ServiceRemotingListener qui ne prend les appels RPC que sur les réplicas primaires, et un deuxième écouteur personnalisé qui prend les demandes de lecture sur les réplicas secondaires :

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(initParams =>
            new MyCustomListener(initParams),
            "customReadonlyEndpoint",
            true),

        new ServiceReplicaListener(initParams =>
            new ServiceRemotingListener<IMyStatefulInterface2>(initParams, this),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

Pour terminer, décrivez les points de terminaison qui sont requis pour le service dans le [manifeste de service](service-fabric-application-model.md), dans la section consacrée aux points de terminaison.

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

Pour une description complète de l’écriture d’un `ICommunicationListener`, consultez la rubrique [Prise en main des services d'API Web de Microsoft Azure Service Fabric avec l'auto-hébergement OWIN](service-fabric-reliable-services-communication-webapi.md)

## Communication du client au service
L'API de services fiables fournit les abstractions suivantes qui facilitent l'écriture des clients pour la communication avec les services.

### ServicePartitionResolver
Cette classe d’utilitaires permet aux clients d'identifier le point de terminaison d'un service Service Fabric lors de l'exécution. Le processus permettant de déterminer le point de terminaison d'un service est appelé résolution de point de terminaison dans la terminologie Service Fabric.

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

### CommunicationClientFactory
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
    protected override void AbortClient(MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

### ServicePartitionClient
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
* [Appel de procédure distante avec Reliable Services à distance](service-fabric-reliable-services-communication-remoting.md)

* [API Web avec OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Communication WCF avec Reliable Services](service-fabric-reliable-services-communication-wcf.md)

 

<!---HONumber=Nov15_HO4-->