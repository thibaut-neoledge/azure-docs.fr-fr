<properties
   pageTitle="Présentation de la communication Reliable Services | Microsoft Azure"
   description="Présentation du modèle de communication Reliable Services, notamment de l’ouverture d’écouteurs sur les services, de la résolution des points de terminaison et de la communication entre les services."
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

# Modèle de communication Reliable Services

Azure Service Fabric, en tant que plateforme, est totalement indépendant de la communication entre les services. Tous les protocoles et toutes les piles sont acceptables, de UDP à HTTP. C'est au développeur de services de choisir comment les services doivent communiquer. L’infrastructure d’application Reliable Services fournit quelques piles et outils de communication prédéfinis que vous pouvez utiliser pour restaurer votre pile de communication personnalisée. Ceux-ci incluent des abstractions que les clients peuvent utiliser pour détecter des points de terminaison de service et communiquer avec eux.

## Configurer la communication de service

L'API Reliable Services utilise une interface simple pour la communication de service. Pour ouvrir un point de terminaison pour votre service, implémentez simplement cette interface :

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Vous pouvez ensuite ajouter votre implémentation d’écouteur de communication en la retournant dans une substitution de méthode de classe de base de services.

Pour des services sans état :

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

Pour des services avec état :

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

Dans les deux cas, vous retournez une collection d’écouteurs. Cela permet à votre service d’utiliser facilement plusieurs écouteurs. Par exemple, vous pouvez avoir un écouteur HTTP et un écouteur WebSocket distinct. Chaque écouteur obtient un nom et la collection qui résulte des paires *nom : adresse* est représentée en tant qu’objet JSON quand un client demande les adresses d’écoute pour une instance de service ou une partition.

Dans un service sans état, la substitution retourne une collection de ServiceInstanceListeners. Un ServiceInstanceListener contient une fonction pour créer un ICommunicationListener et lui donner un nom. Pour les services avec état, la substitution retourne une collection de ServiceReplicaListener. Cela diffère légèrement de son homologue sans état, car un ServiceReplicaListener a une option pour ouvrir un ICommunicationListener sur des réplicas secondaires. Non seulement vous pouvez utiliser plusieurs écouteurs de communication dans un service, mais vous pouvez également spécifier ceux qui acceptent les demandes sur les réplicas secondaires, et ceux qui écoutent uniquement sur les réplicas principaux.

Par exemple, vous pouvez avoir un ServiceRemotingListener qui ne prend les appels RPC que sur les réplicas principaux, et un second écouteur personnalisé qui prend les demandes de lecture sur les réplicas secondaires :

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

Enfin, vous pouvez décrire les points de terminaison qui sont requis pour le service dans le [manifeste de service](service-fabric-application-model.md), dans la section consacrée aux points de terminaison.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

L’écouteur de communication peut accéder aux ressources de point de terminaison allouées à partir du `CodePackageActivationContext` dans `ServiceInitializationParameters`. L’écouteur peut alors commencer à écouter les demandes quand il est ouvert.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]Les ressources de point de terminaison sont communes au package de service entier et elles sont allouées par Service Fabric à l’activation du package de service. Tous les réplicas hébergés dans le même ServiceHost partagent le même port. Cela signifie que l'écouteur de communication doit prendre en charge le partage de port. La méthode recommandée consiste à configurer l’écouteur de communication afin qu’il utiliser l’ID de la partition et l’ID du réplica/de l’instance lors de la génération de l’adresse d’écoute.

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

Pour une présentation complète de l’écriture d’un `ICommunicationListener`, consultez [Services API Web Service Fabric avec auto-hébergement OWIN](service-fabric-reliable-services-communication-webapi.md).

### Communication entre client et service
L’API Reliable Services fournit les abstractions suivantes pour faciliter l’écriture de clients qui communiquent avec des services.

#### ServicePartitionResolver
Cette classe d’utilitaires permet aux clients d'identifier le point de terminaison d'un service Service Fabric lors de l'exécution. Dans la terminologie Service Fabric, le processus permettant de déterminer le point de terminaison d’un service est appelé *résolution de point de terminaison de service*.

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
> [AZURE.NOTE]FabricClient est l’objet utilisé pour communiquer avec le cluster Service Fabric pour diverses opérations de gestion sur le cluster.

Généralement, le code client ne doit pas fonctionner directement avec `ServicePartitionResolver`. Il est créé et transmis à d’autres classes d’assistance de l’API Reliable Services. Ces classes utilisent le programme de résolution en interne et aident le client à communiquer avec le service.

#### CommunicationClientFactory
`ICommunicationClientFactory` définit l’interface de base implémentée par une fabrique de clients de communication qui produit des clients capables de communiquer avec un service Service Fabric. L’implémentation de CommunicationClientFactory dépend de la pile de communication utilisée par le service Service Fabric avec lequel le client veut communiquer. L’API Reliable Services fournit un `CommunicationClientFactoryBase<TCommunicationClient>`. Celui-ci fournit une implémentation de base de l’interface `ICommunicationClientFactory` et effectue des tâches communes à toutes les piles de communication. (Ces tâches incluent l’utilisation d’un `ServicePartitionResolver` pour déterminer le point de terminaison de service). Les clients implémentent généralement la classe abstraite CommunicationClientFactoryBase pour gérer la logique spécifique de la pile de communication.

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

#### ServicePartitionClient
`ServicePartitionClient` utilise CommunicationClientFactory (et ServicePartitionResolver en interne). Cela facilite également la communication avec le service en gérant les nouvelles tentatives de communication courante et les exceptions temporaires de Service Fabric.

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

Un modèle d’utilisation type ressemble à ceci :

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
* [Appels de procédure distante avec Reliable Services à distance](service-fabric-reliable-services-communication-remoting.md)

* [API Web qui utilise OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Communication WCF à l’aide de Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0121_2016-->