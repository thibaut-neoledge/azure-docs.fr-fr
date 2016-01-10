<properties
   pageTitle="Communication de service avec l’API web ASP.NET | Microsoft Azure"
   description="Découvrez comment implémenter une communication de service étape par étape à l’aide de l’API web ASP.NET avec auto-hébergement OWIN dans l’API Reliable Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Prise en main des services d'API Web de Microsoft Azure Service Fabric avec l'auto-hébergement OWIN

Service Fabric met la puissance entre vos mains lorsque vous devez choisir comment vos services communiqueront avec les utilisateurs et entre eux. Ce didacticiel se concentre sur l'implémentation de la communication de service à l'aide de l'API Web ASP.NET avec l'auto-hébergement OWIN dans l'API de *services fiables* Service Fabric. Nous examinerons en détail l'API de communication enfichable *Services fiables* et vous montrerons étape par étape comment configurer un écouteur de communication personnalisé pour votre service en utilisant l'API Web comme exemple. Pour voir un exemple complet d'écouteur de communication d'API Web, consultez l'[exemple WebApplication Service Fabric sur GitHub](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/WebApplication).


## Introduction à l'API Web dans Service Fabric

L'API Web ASP.NET est une infrastructure populaire et puissante pour la création d'API HTTP sur l'infrastructure .NET. Rendez-vous sur [www.asp.net/webapi](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) pour en savoir plus sur l'API Web si vous n'êtes pas déjà familier avec celle-ci.

L'API Web dans Service Fabric est la même API Web ASP.NET que vous connaissez et appréciez. La différence réside dans la manière dont vous *héberger* une application API Web (indice : vous n'utiliserez pas IIS). Pour mieux comprendre la différence, nous allons la décomposer en deux parties :

 1. L'application API Web (vos contrôleurs, modèles, etc.)
 2. L'hôte (le serveur web, généralement IIS)

L'application API Web elle-même ne change pas ici - elle ne diffère pas des applications API Web que vous avez écrites dans le passé, et vous devriez pouvoir y insérer directement la majeure partie de votre code d'application. L'hébergement de l'application peut être légèrement différent de l'hébergement sur IIS auquel vous êtes habitué. Mais avant de passer à la partie sur l'hébergement, commençons par la partie la plus connue : l'application API Web.


## Création de l'application

Commencez par créer une application Service Fabric, avec un seul service sans état, dans Visual Studio 2015 :

![Création d'une application Service Fabric](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

![Création d'un service unique sans état](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

Cela nous donne un service sans état vide qui hébergera l'application API Web. Nous allons configurer l'application à partir de zéro pour voir comment elle est assemblée.

La première étape consiste à extraire certains packages NuGet pour l'API Web. Le package que nous souhaitons utiliser est **Microsoft.AspNet.WebApi.OwinSelfHost**. Ce package comprend tous les packages API Web et les packages *hôtes* - cela sera important plus tard.

![](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Avec les packages installés, nous pouvons commencer la création de la structure de base du projet d'API Web. Si vous avez déjà utilisé des API Web, la structure du projet doit sembler très familière. Commencez par créer les répertoires de base de l'API Web :

 + App\_Start
 + Controllers
 + Models

Ajoutez les classes de configuration de base de l'API Web dans le répertoire App\_Start. Pour l'instant, nous allons simplement ajouter une configuration de formateur de type média vide :

 + FormatterConfig.cs

```csharp

namespace WebApiService
{
    using System.Net.Http.Formatting;

    public static class FormatterConfig
    {
        public static void ConfigureFormatters(MediaTypeFormatterCollection formatters)
        {
        }
    }
}

```

Ajoutez un contrôleur par défaut dans le répertoire Contrôleurs :

 + DefaultController.cs

```csharp

namespace WebApiService.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    [RoutePrefix("api")]
    public class DefaultController : ApiController
    {
        // GET api/values
        [Route("values")]
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        [Route("values/{id}")]
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        [Route("values")]
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        [Route("values/{id}")]
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        [Route("values/{id}")]
        public void Delete(int id)
        {
        }
    }
}

```

Enfin, ajoutez une classe de démarrage à la racine du projet pour inscrire le routage, les formateurs et tout autre programme d'installation de configuration. C'est également depuis cet endroit que l'API Web se connecte à l'*hôte*, ce que nous reverrons ultérieurement. Lors du paramétrage de la classe de démarrage, créez une interface appelée *IOwinAppBuilder* pour la classe de démarrage qui définit la méthode de configuration. Même si elle n'est pas techniquement nécessaire au fonctionnement de l'API Web, elle offrira plus de souplesse d'utilisation de la classe de démarrage.

 + Startup.cs

```csharp

namespace WebApiService
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            config.MapHttpAttributeRoutes();
            FormatterConfig.ConfigureFormatters(config.Formatters);

            appBuilder.UseWebApi(config);
        }
    }
}

```

 + IOwinAppBuilder.cs

```csharp

namespace WebApiService
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

C'est tout pour la partie de l'application. À ce stade, nous avons simplement configuré la mise en page du projet de base de l'API Web. Comparé aux projets d'API Web que vous avez écrits dans le passé ou au modèle API Web de base, ce projet ne devrait pas très différent jusqu'à présent. Votre logique métier est appliquée comme d'habitude aux contrôleurs et aux modèles.

Maintenant que devons-nous faire au niveau de l'hébergement pour pouvoir l'exécuter ?


## Hébergement du service

Dans Service Fabric, votre service s'exécute dans un *processus hôte de service* - un fichier exécutable qui exécute le code de votre service. Lors de l'écriture d'un service à l'aide de l'API de services fiables, et en fait dans la plupart des cas, lorsque vous écrivez un service sur Service Fabric dans .NET, votre projet de service compile uniquement à un fichier .EXE qui enregistre votre type de service et exécute votre code. En fait, si vous ouvrez **Program.cs** dans le projet de service sans état, vous devriez voir :

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType("WebApiServiceType", typeof(Service));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e);
            throw;
        }
    }
}

```

Si cela ressemble étrangement au point d'entrée d'une application console, c'est parce c'est le cas.

Nous n'aborderons pas dans cet article le processus hôte du service ni l'inscription du service, mais il est important de savoir que **votre code de service s'exécute dans son propre processus**.

## Auto-hébergement de l'API Web avec un hôte OWIN

Étant donné que le code de votre application d'API Web est hébergé dans son propre processus, comment se connecter à un serveur web ? Entrez [OWIN](http://owin.org/). OWIN est simplement un contrat entre les applications web .NET et les serveurs web. Traditionnellement avec ASP.NET - jusqu'à MVC 5 - l'application web était étroitement liée à IIS via System.Web. Toutefois, l'API Web implémente OWIN, qui vous permet d'écrire une application web dissociée du serveur web qui l'héberge. Cela vous permet d'utiliser un serveur web OWIN*auto-hébergé* que vous pouvez démarrer dans votre propre processus, qui s'intègre parfaitement au modèle d'hébergement Service Fabric que nous venons de décrire.

Dans cet article, nous utiliserons Katana comme hôte OWIN pour l'application de l'API Web. Katana est une implémentation hôte OWIN open source.

> [AZURE.NOTE]Pour en savoir plus sur Katana, rendez-vous sur le [site Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana), et pour une rapide présentation rapide de l'utilisation de Katana pour auto-héberger l'API Web, consultez cet article sur l'[utilisation d'OWIN pour auto-héberger ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## Configurer le serveur web

L'API Reliable Services fournit un point d'entrée de communication dans lequel vous pouvez connecter des piles de communication pour permettre à des utilisateurs et à des clients de se connecter au service :

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Le serveur web - et toute autre pile de communication que vous pouvez utiliser dans le futur, par exemple WebSockets - doit utiliser l'interface ICommunicationListener pour s'intégrer correctement au système. Les raisons de cette mesure deviendront plus évidentes dans les étapes suivantes.

Commencez par créer une classe appelée OwinCommunicationListener qui implémente ICommunicationListener :

 + OwinCommunicationListener.cs:

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}

```

L'interface ICommunicationListener fournit trois méthodes permettant de gérer un écouteur de communication pour votre service :

 + **OpenAsync**: commencer à écouter les demandes.
 + **CloseAsync**: interrompre l'écoute des demandes, terminer toutes les demandes en cours et s'arrêter correctement.
 + **Abort**: tout annuler et s'arrêter immédiatement.

Pour commencer, ajoutez des membres de classe privés pour un préfixe de chemin d'URL et la classe de **démarrage** créée précédemment. Ces éléments seront initialisés via le constructeur et utilisés ultérieurement lorsque vous configurez l'URL d'écoute. Ajoutez également des membres de classe privés pour enregistrer l'adresse d'écoute et le handle de serveur créés pendant l'initialisation et ultérieurement lorsque le serveur est démarré, respectivement.

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;
    private readonly ServiceInitializationParameters serviceInitializationParameters;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
    {
        this.startup = startup;
        this.appRoot = appRoot;
        this.serviceInitializationParameters = serviceInitializationParameters;
    }        

    ...

```

### Implémentation

Pour configurer le serveur Web, nous avons besoin de quelques éléments d'information :

 + **Préfixe du chemin de l'URL**. Bien que facultatif, il est judicieux de le configurer maintenant pour pouvoir héberger en toute sécurité plusieurs services web dans votre application.
 + **Un port**.

Avant de sélectionner un port pour le serveur web, il est important de comprendre que Service Fabric fournit une couche d'application qui agit comme un tampon entre votre application et le système d'exploitation sous-jacent sur lequel il s'exécute. Par conséquent, Service Fabric permet de configurer des *points de terminaison* pour vos services. Service Fabric s'assure que le système d'extrémité est disponible pour votre service, ce qui vous évite de le configurer vous-même avec l'environnement de système d'exploitation sous-jacent. Cela vous permet d'héberger facilement votre application Service Fabric dans différents environnements sans avoir à la modifier (par exemple, vous pouvez héberger la même application dans Azure ou dans votre propre centre de données).

Configurer un point de terminaison HTTP dans PackageRoot\\ServiceManifest.xml :

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

Cette étape est importante car le processus hôte du service s'exécute avec des informations d'identification limitées (service réseau sous Windows), ce qui signifie que votre service ne pourra pas accéder lui-même à un point de terminaison HTTP. Grâce à la configuration de point de terminaison, Service Fabric est capable de configurer l'ACL appropriée pour l'URL que le service écoutera tout en fournissant un emplacement standard pour configurer les systèmes d'extrémité.


Dans OwinCommunicationListener.cs, nous pouvons commencer à implémenter OpenAsync. C’est ici que nous démarrons le serveur Web. Tout d'abord, récupérez les informations du point de terminaison et créez l'URL sur laquelle le service écoutera.

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = String.Format(
        CultureInfo.InvariantCulture,
        "http://+:{0}/{1}",
        port,
        String.IsNullOrWhiteSpace(this.appRoot)
            ? String.Empty
            : this.appRoot.TrimEnd('/') + '/');
    ...

```

Notez que « http://+ » est utilisé ici. Cette opération permet de s'assurer que le serveur web écoute sur toutes les adresses disponibles, y compris sur l'hôte local, le nom de domaine complet et l'adresse IP de l'ordinateur.

L'implémentation d'OpenAsync est une des principales raisons pour laquelle le serveur web (ou une pile de communication) est implémenté comme élément ICommunicationListener au lieu d'être simplement ouvert directement à partir de RunAsync() dans le service. La valeur de retour d'OpenAsync est l'adresse sur laquelle le serveur web écoute. Lorsque cette adresse est renvoyée au système, elle enregistre auprès du service. Service Fabric fournit une API qui permet aux clients ou autres services de rechercher cette adresse par nom de service. C'est important car l'adresse du service n'est pas statique puisque les services sont déplacés dans le cluster à des fins d'équilibrage des ressources et de disponibilité. Ce mécanisme permet aux clients de résoudre l'adresse d'écoute pour un service.

Dans cette optique, OpenAsync démarre le serveur web et retourne l'adresse qu'il écoute. Notez qu'il écoute sur « http://+ », mais avant de retourner l'adresse, le « + » est remplacé par l'adresse IP ou le nom de domaine complet du nœud en cours. La raison est que cette adresse retournée par la méthode représente ce qui est enregistré dans le système, et c'est ce que les clients et les autres services verront s'ils recherchent l'adresse du service. Pour pouvoir s'y connecter, les clients ont besoin de l'IP ou du nom de domaine complet de l'adresse.

```csharp

    ...

    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
    string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

    return Task.FromResult(publishAddress);
}

```

Notez que cela fait référence à la classe de **démarrage** transmise à OwinCommunicationListener dans le constructeur. Cette instance de démarrage est utilisée par le serveur web pour démarrer l'application de l'API Web.

La ligne ServiceEventSource.Current.Message() s'affichera ultérieurement dans la fenêtre des événements de diagnostic lorsque vous exécutez l'application pour vous informer que le serveur web a bien démarré.

### CloseAsync et Abort

Enfin, implémentez à la fois CloseAsync et Abort pour arrêter le serveur web. Le serveur web peut être arrêté en supprimant le handle de serveur créé pendant OpenAsync.

```csharp

public Task CloseAsync(CancellationToken cancellationToken)
{
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}

```

Dans cet exemple d'implémentation, CloseAsync et Abort arrêtent tout simplement le serveur web. Vous pouvez choisir d'effectuer un arrêt coordonné plus approprié du serveur web dans CloseAsync ; par exemple, en attente que les demandes en cours se terminent avant de les renvoyer.

## Démarrer le serveur web

Vous êtes maintenant prêt à créer et à renvoyer une instance OwinCommunicationListener pour démarrer le serveur web. Retournez dans la classe de service (Service.cs) et remplacez la méthode **CreateServiceInstanceListeners()** :

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new[]
    {
        new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
    };
}

```

C'est ici que finissent par se rencontrer l'*application* de l'API Web et l'*hôte* OWIN : l'*hôte* (**OwinCommunicationListener**) reçoit une instance de l'*application* (API Web via **Startup**), et Service Fabric gère son cycle de vie. Ce même modèle peut généralement être suivi d'une pile de communication.

## Exemple complet

Dans cet exemple, vous n'avez rien d'autre à faire dans la méthode RunAsync() et ce remplacement peut être simplement supprimé.

L'implémentation finale du service doit être très simple car il suffit de créer l'écouteur de communication :

```csharp

namespace WebApiService
{
    using System.Collections.Generic;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;
    using Microsoft.ServiceFabric.Services.Runtime;

    public class WebApiService : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            return new[]
            {
                new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
            };
        }
    }
}

```

Voici la classe `OwinCommunicationListener` complète :

```csharp

namespace WebApiService
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private readonly ServiceInitializationParameters serviceInitializationParameters;
        private IDisposable serverHandle;
        private string listeningAddress;
        
        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
        {
            this.startup = startup;
            this.appRoot = appRoot;
            this.serviceInitializationParameters = serviceInitializationParameters;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
            int port = serviceEndpoint.Port;

            this.listeningAddress = String.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/{1}",
                port,
                String.IsNullOrWhiteSpace(this.appRoot)
                    ? String.Empty
                    : this.appRoot.TrimEnd('/') + '/');

            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
            string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

            return Task.FromResult(publishAddress);
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            ServiceEventSource.Current.Message("Close");

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            ServiceEventSource.Current.Message("Abort");

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.serverHandle != null)
            {
                try
                {
                    this.serverHandle.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

Tous les composants sont en place, votre projet devrait maintenant ressembler à une application API Web typique avec les points d'entrée de l'API de services fiables et un hôte OWIN :


![](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Exécuter et se connecter via un navigateur web

Si ce n'est déjà fait, [configurez votre environnement de développement](service-fabric-get-started.md).


Vous pouvez désormais générer et déployer votre service. Appuyez sur **F5** dans Visual Studio pour générer et déployer l'application. Dans la fenêtre des événements de diagnostic, vous devriez voir un message indiquant que le serveur web est ouvert sur **http://localhost:80/webapp/api**


![](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]Si le port est déjà ouvert par un autre processus sur votre ordinateur, vous pouvez rencontrer une erreur indiquant que l'écouteur n'a pas pu être ouvert. Dans ce cas, essayez d'utiliser un autre port dans la configuration du système d'extrémité du fichier ServiceManifest.xml.


Une fois le service exécuté, ouvrez un navigateur et accédez à [http://localhost/webapp/api/values](http://localhost/webapp/api/values) pour le tester.

## Montée en charge

La montée en charge d'applications web (Web Apps) sans état implique généralement l'ajouter d'ordinateurs supplémentaires sur lesquels l'application web sera exécutée. Le moteur d'orchestration de Service Fabric peut réaliser cette opération chaque fois que de nouveaux nœuds sont ajoutés à un cluster. Lorsque vous créez des instances de service sans état, vous pouvez spécifier le nombre d'instances à générer. Service Fabric placera ce nombre d'instances sur les nœuds du cluster en conséquence, en veillant à ne pas créer plus d'une instance sur chaque nœud. Vous pouvez également demander à Service Fabric de toujours créer une instance sur chaque nœud en spécifiant « -1 » comme nombre d'instances. Cela garantit que chaque fois que vous ajoutez des nœuds pour faire évoluer votre cluster, une instance de votre service sans état sera créée sur les nouveaux nœuds. Cette valeur étant une propriété de l'instance de service, elle est définie lors de la création d'une instance de service via PowerShell :

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Ou lors de la définition d'un service par défaut dans un projet de service sans état Visual Studio :

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Pour plus d'informations sur la création d'applications et d'instances de services, voir [Comment déployer et supprimer des applications](service-fabric-deploy-remove-applications.md).

## ASP.NET 5

Dans ASP.NET 5, le concept et le modèle de programmation liés à la séparation de l'*application* de l'*hôte* dans les applications web restent identiques. Ils peuvent également être appliqués à d'autres formes de communication. En outre, bien que l'*hôte* puisse différer dans l'API Web ASP.NET 5, l'*application* API Web reste la même, et c'est ici que réside l'essentiel de la logique d'application.

## Étapes suivantes

[Débogage de votre application Service Fabric dans Visual Studio](service-fabric-debugging-your-application.md)

<!----HONumber=Nov15_HO4-->