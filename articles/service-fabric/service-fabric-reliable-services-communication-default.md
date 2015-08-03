<properties
   pageTitle="Pile de communication par défaut fournie par Service Fabric"
   description="Cet article décrit la pile de communication par défaut fournie par l'infrastructure de services fiables permettant aux services et aux clients de communiquer."
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
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# Pile de communication par défaut fournie par l'infrastructure de services fiables
Pour les auteurs de service qui ne sont pas liés à une implémentation de pile de communication particulière (WebAPI, WCF etc.), l'infrastructure fournit des éléments de communication côté Client et Service qui peuvent être utilisés pour établir la communication entre le Client et le Service.

> [AZURE.NOTE]Veuillez installer les derniers packages NuGet pour obtenir les fonctionnalités mentionnées ci-dessous.

## Écouteur de communication de service
L'écouteur de communication par défaut pour le service est implémenté dans la classe `ServiceCommunicationListener`

```csharp

public class ServiceCommunicationListener<TServiceImplementation> : ICommunicationListener where TServiceImplementation : class
{
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType);
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType, string endpointResourceName);

    public void Abort();
    public Task CloseAsync(CancellationToken cancellationToken);
    public void Initialize(ServiceInitializationParameters serviceInitializationParameters);
    public Task<string> OpenAsync(CancellationToken cancellationToken);
}

```
Les méthodes que le service implémente et souhaite présenter à ses clients sont définies comme des méthodes asynchrones dans une interface qui hérite de l'interface `IService`. Le service peut ensuite simplement instancier l'objet `ServiceCommunicationListener` et le renvoyer dans la méthode [`CreateCommunicationListener`](service-fabric-reliable-services-communication.md). Par exemple, le code de service HelloWorld pour configurer cette pile de communication peut être défini comme suit.

```csharp

[DataContract]
public class Message
{
    [DataMember]
    public string Content;
}

public interface IHelloWorld : IService
{
    Task<Message> GetGreeting();
}

public class HelloWorldService : StatelessService, IHelloWorld
{
    public const string ServiceTypeName = "HelloWorldUsingDefaultCommunicationType";

    private Message greeting = new Message() { Content = "Default greeting" };

    protected override ICommunicationListener CreateCommunicationListener()
    {
        return new ServiceCommunicationListener<HelloWorldService>(this);
    }

    public Task<Message> GetGreeting()
    {
        return Task.FromResult(this.greeting);
    }
}

```
> [AZURE.NOTE]Les arguments et les types de retour dans l'interface Service, par exemple la classe Message ci-dessus, devraient être sérialisables par l'élément .net [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).


## Écriture de clients pour communiquer avec ServiceCommunicationListener
Pour que les clients puissent communiquer avec les services en utilisant l'élément `ServiceCommunicationListener`, l'infrastructure fournit une classe `ServiceProxy`.

```csharp

public abstract class ServiceProxy : IServiceProxy
{
...

    public static TServiceInterface Create<TServiceInterface>(Uri serviceName);

...
}

```

Les clients peuvent instancier un objet proxy de service qui implémente l'interface Service correspondante et appelle des méthodes sur l'objet proxy.

```csharp

var helloWorldClient = ServiceProxy.Create<IHelloWorld>(helloWorldServiceName);

var message = await helloWorldClient.GetGreeting();

Console.WriteLine("Greeting is {0}", message.Content);


```

>[AZURE.NOTE]L'infrastructure de communication gère la propagation des exceptions survenues au niveau du service au client. Par conséquent, la logique de gestion des exceptions au niveau du client à l'aide de ServiceProxy peut directement traiter les exceptions susceptibles d'être générées par le service.
 

<!---HONumber=July15_HO4-->