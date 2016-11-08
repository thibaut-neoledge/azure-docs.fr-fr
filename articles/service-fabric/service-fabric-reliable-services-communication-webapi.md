---
title: Communication de service avec l’API Web ASP.NET | Microsoft Docs
description: Découvrez comment implémenter progressivement une communication de service à l’aide de l’API Web ASP.NET avec auto-hébergement OWIN dans l’API Reliable Services.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 07/29/2016
ms.author: vturecek

---
# Prise en main : services de l’API Web Service Fabric avec auto-hébergement OWIN
Azure Service Fabric vous laisse librement choisir comment vos services communiqueront avec les utilisateurs et entre eux. Ce didacticiel se concentre sur l’implémentation de la communication de service à l’aide de l’API Web ASP.NET avec auto-hébergement OWIN (Open Web Interface for .NET) dans l’API Reliable Services de Service Fabric. Nous allons approfondir l’étude de l’API de communication enfichable Reliable Services. Nous utiliserons également l’API Web dans un exemple pas à pas pour vous montrer comment configurer un écouteur de communication personnalisé.

## Présentation de l’API Web dans Service Fabric
L'API Web ASP.NET est une infrastructure populaire et puissante pour la création d'API HTTP sur l'infrastructure .NET. Si vous ne connaissez pas déjà cette infrastructure, consultez [Prise en main d’API Web ASP.NET 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) pour en savoir plus.

L'API Web dans Service Fabric est la même API Web ASP.NET que vous connaissez et appréciez. La différence réside dans la manière d’*héberger* une application API Web. Vous n’allez pas utiliser Microsoft Internet Information Services (IIS). Pour mieux comprendre la différence, nous allons la décomposer en deux parties :

1. L’application API Web (y compris les contrôleurs et les modèles)
2. L'hôte (le serveur web, généralement IIS)

Une application API Web elle-même ne change pas. Elle ne diffère pas des applications API Web que vous avez éventuellement écrites par le passé, et vous devriez pouvoir facilement réutiliser la plupart du code de votre application. Mais si vous avez utilisé un hébergement sur IIS, l’emplacement auquel vous hébergez l’application risque d’être légèrement différent de celui auquel vous êtes habitué. Avant de passer à la partie sur l’hébergement, commençons par la partie la plus connue : l’application API Web.

## Création de l'application
Commencez par créer une application Service Fabric, avec un seul service sans état, dans Visual Studio 2015 :

![Création d'une application Service Fabric](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Vous avez à votre disposition un modèle Visual Studio pour un service sans état utilisant une API Web. Dans ce didacticiel, nous allons créer un projet d’API Web qui donnera le même résultat que si vous aviez sélectionné ce modèle.

Sélectionnez un projet Service sans état vide pour découvrir comment créer un projet d’API Web, ou vous pouvez commencer avec le modèle d’API Web de service sans état et suivre simplement la procédure indiquée.

![Création d'un service unique sans état](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

La première étape consiste à extraire certains packages NuGet pour l'API Web. Le package que nous voulons utiliser est Microsoft.AspNet.WebApi.OwinSelfHost. Ce package comprend tous les packages d’API Web et les packages *hôtes* nécessaires. Ils auront leur importance plus tard.

![Créer l’API Web à l’aide du Gestionnaire de package NuGet](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Avec les packages installés, vous pouvez commencer à créer la structure de base du projet d’API Web. Si vous avez déjà utilisé des API Web, la structure du projet doit sembler très familière. Commencez par ajouter un répertoire `Controllers` et un contrôleur de valeurs simple :

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;

namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

Ensuite, ajoutez une classe de démarrage à la racine du projet pour inscrire le routage, les formateurs et tout autre programme d’installation de configuration. C'est également depuis cet endroit que l'API Web se connecte à l'*hôte*, ce que nous reverrons ultérieurement.

**Startup.cs**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

C'est tout pour la partie de l'application. À ce stade, nous avons simplement configuré la disposition du projet de base de l’API Web. Jusqu’ici, comparé aux projets d’API Web que vous avez éventuellement écrits par le passé ou au modèle d’API Web de base, ce projet ne doit pas avoir l’air très différent. Votre logique métier est appliquée comme d'habitude aux contrôleurs et aux modèles.

Maintenant que devons-nous faire au niveau de l’hébergement pour pouvoir l’exécuter ?

## Hébergement du service
Dans Service Fabric, votre service s’exécute dans un *processus hôte de service*, c’est-à-dire un fichier exécutable qui exécute le code de votre service. Quand vous écrivez un service à l’aide de l’API Reliable Services, votre projet de service se compile juste en un fichier exécutable qui inscrit votre type de service et exécute votre code. Ceci se vérifie dans la plupart des cas quand vous écrivez un service sur Service Fabric dans .NET. Quand vous ouvrez Program.cs dans le projet de service sans état, vous devriez voir :

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Si cela ressemble étrangement au point d'entrée d'une application console, c'est parce c'est le cas.

Il n’appartient pas à cet article de donner plus de détails sur le processus hôte de service et l’inscription du service. Mais il est important de savoir à ce stade que *votre code de service s’exécute dans son propre processus*.

## Auto-héberger l’API Web avec un hôte OWIN
Étant donné que le code de votre application API Web est hébergé dans son propre processus, comment se connecter à un serveur web ? Entrez [OWIN](http://owin.org/). OWIN est simplement un contrat entre les applications web .NET et les serveurs web. Traditionnellement avec ASP.NET (jusqu’à MVC 5), l’application web est étroitement liée à IIS via System.Web. Toutefois, l’API Web implémente OWIN, ce qui vous permet d’écrire une application web dissociée du serveur web qui l’héberge. Pour cette raison, vous pouvez utiliser un serveur web OWIN *auto-hébergé* que vous pouvez démarrer dans votre propre processus. Il s’intègre parfaitement avec le modèle d’hébergement Service Fabric que nous venons de décrire.

Dans cet article, nous utiliserons Katana comme hôte OWIN pour l'application de l'API Web. Katana est une implémentation d’hôte OWIN open source basée sur [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx) et sur l’[API de serveur HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx) de Windows.

> [!NOTE]
> Pour en savoir plus sur Katana, accédez au [site Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Pour obtenir un aperçu rapide de l’utilisation de Katana pour auto-héberger l’API Web, consultez [Use OWIN to Self-Host ASP.NET Web API (Utiliser OWIN pour auto-héberger une API Web ASP.NET 2)](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).
> 
> 

## Configurer le serveur web
L’API Reliable Services fournit un point d’entrée de communication dans lequel vous pouvez connecter des piles de communication pour permettre à des utilisateurs et à des clients de se connecter au service :

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Le serveur web (et toute autre pile de communication que vous pouvez utiliser à l’avenir, comme WebSockets) doit utiliser l’interface ICommunicationListener pour s’intégrer correctement au système. Les raisons de cette mesure deviendront plus évidentes dans les étapes suivantes.

Commencez par créer une classe appelée OwinCommunicationListener qui implémente ICommunicationListener :

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
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

L'interface ICommunicationListener fournit trois méthodes permettant de gérer un écouteur de communication pour votre service :

* *OpenAsync*. Commence à écouter les demandes.
* *CloseAsync*. Stoppe l’écoute des demandes, termine toutes les demandes en cours et s’arrête correctement.
* *Abort*. Annule tout et s’arrête immédiatement.

Pour commencer, ajoutez les membres de classe privée pour les éléments dont l’écouteur a besoin pour fonctionner. Ces éléments sont initialisés par le biais du constructeur et utilisés ultérieurement quand vous configurez l’URL d’écoute.

```csharp
public class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }


    ...

```

## Implémenter OpenAsync
Pour configurer le serveur web, vous avez besoin de deux informations :

* *Préfixe du chemin de l'URL*. Bien que facultatif, il est judicieux de le configurer maintenant pour pouvoir héberger en toute sécurité plusieurs services web dans votre application.
* *Un port*.

Avant d’obtenir un port pour le serveur web, il est important de comprendre que Service Fabric fournit une couche Application qui sert de tampon entre votre application et le système d’exploitation sous-jacent sur lequel elle s’exécute. Par conséquent, Service Fabric permet de configurer des *points de terminaison* pour vos services. Service Fabric garantit la disponibilité des points de terminaison pour votre service. Ainsi, vous n’êtes pas obligé de les configurer vous-même dans l’environnement du système d’exploitation sous-jacent. Vous pouvez facilement héberger votre application Service Fabric dans différents environnements sans avoir à apporter de modifications à votre application. (Par exemple, vous pouvez héberger la même application dans Azure ou dans votre propre centre de données).

Configurer un point de terminaison HTTP dans PackageRoot\\ServiceManifest.xml :

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

Cette étape est importante car le processus hôte de service s’exécute sous des informations d’identification limitées (Service réseau sous Windows). Cela signifie que votre service n’a pas d’accès pour configurer un point de terminaison HTTP tout seul. En utilisant la configuration du point de terminaison, Service Fabric sait comment configurer la liste de contrôle d’accès (ACL) pour l’URL que le service va écouter. Service Fabric fournit également un emplacement standard pour configurer des points de terminaison.

De retour dans OwinCommunicationListener.cs, vous pouvez commencer à implémenter OpenAsync. C’est ici que vous démarrez le serveur web. Tout d'abord, récupérez les informations du point de terminaison et créez l'URL sur laquelle le service écoutera. L’URL sera différente selon que l’écouteur est utilisé dans un service sans état ou un service avec état. Pour un service avec état, l’écouteur doit créer une adresse unique pour chaque réplica de service avec état qu’il écoute. Pour les services sans état, l’adresse peut être beaucoup plus simple.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }

    ...

```

Notez que « http://+ » est utilisé ici. Cette opération permet de s’assurer que le serveur web écoute toutes les adresses disponibles, y compris l’hôte local, le nom de domaine complet et l’adresse IP de l’ordinateur.

L’implémentation d’OpenAsync est l’une des principales raisons pour lesquelles le serveur web (ou une pile de communication) est implémenté comme élément ICommunicationListener au lieu d’être simplement ouvert directement à partir de `RunAsync()` dans le service. La valeur de retour d'OpenAsync est l'adresse sur laquelle le serveur web écoute. Lorsque cette adresse est renvoyée au système, elle enregistre auprès du service. Service Fabric fournit une API qui permet aux clients et à d’autres services de rechercher cette adresse par nom de service. Ceci est important, car l’adresse du service n’est pas statique. Les services se déplacent dans le cluster à des fins d’équilibrage des ressources et de disponibilité. Ce mécanisme permet aux clients de résoudre l'adresse d'écoute pour un service.

Dans cette optique, OpenAsync démarre le serveur web et retourne l’adresse qu’il écoute. Notez qu’il écoute « http://+ », mais avant qu’OpenAsync ne retourne l’adresse, le « + » est remplacé par l’adresse IP ou le nom de domaine complet du nœud actuel. L’adresse retournée par cette méthode est celle enregistrée dans le système. Il s’agit également de celle que les clients et autres services voient quand ils recherchent l’adresse d’un service. Pour pouvoir s'y connecter, les clients ont besoin de l'IP ou du nom de domaine complet de l'adresse.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.Message("Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.Message("Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

Notez que ces derniers font référence à la classe de démarrage transmise à OwinCommunicationListener dans le constructeur. Cette instance de démarrage est utilisée par le serveur web pour amorcer l’application API Web.

La ligne `ServiceEventSource.Current.Message()` apparaît ultérieurement dans la fenêtre Événements de diagnostic, quand vous exécutez l’application pour confirmer que le serveur web a correctement démarré.

## Implémenter CloseAsync et Abort
Enfin, implémentez à la fois CloseAsync et Abort pour arrêter le serveur web. Le serveur web peut être arrêté en supprimant le handle de serveur créé pendant OpenAsync.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.webApp != null)
    {
        try
        {
            this.webApp.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

Dans cet exemple d’implémentation, CloseAsync et Abort arrêtent tout simplement le serveur web. Vous pouvez choisir d’effectuer un arrêt plus élégamment coordonné du serveur web dans CloseAsync. Par exemple, l’arrêt peut attendre que les demandes en cours d’exécution soient terminées avant le retour.

## Démarrer le serveur web
Vous êtes maintenant prêt à créer et à renvoyer une instance OwinCommunicationListener pour démarrer le serveur web. De retour dans la classe de service (Service.cs), substituez la méthode `CreateServiceInstanceListeners()` :

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

C’est là que l*’application* API Web et l*’hôte* OWIN finissent par se rencontrer. L’hôte (OwinCommunicationListener) reçoit une instance de l*’application* (API Web) via la classe de démarrage. Ensuite, Service Fabric gère son cycle de vie. Ce même modèle peut généralement être suivi d'une pile de communication.

## Assemblage
Dans cet exemple, vous n’avez rien à faire dans la méthode `RunAsync()`. Vous pouvez donc tout simplement supprimer cette substitution.

L’implémentation du service finale doit être très simple. Il suffit de créer l’écouteur de communication :

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

Voici la classe `OwinCommunicationListener` complète :

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        private readonly ServiceEventSource eventSource;
        private readonly Action<IAppBuilder> startup;
        private readonly ServiceContext serviceContext;
        private readonly string endpointName;
        private readonly string appRoot;

        private IDisposable webApp;
        private string publishAddress;
        private string listeningAddress;

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
            : this(startup, serviceContext, eventSource, endpointName, null)
        {
        }

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
        {
            if (startup == null)
            {
                throw new ArgumentNullException(nameof(startup));
            }

            if (serviceContext == null)
            {
                throw new ArgumentNullException(nameof(serviceContext));
            }

            if (endpointName == null)
            {
                throw new ArgumentNullException(nameof(endpointName));
            }

            if (eventSource == null)
            {
                throw new ArgumentNullException(nameof(eventSource));
            }

            this.startup = startup;
            this.serviceContext = serviceContext;
            this.endpointName = endpointName;
            this.eventSource = eventSource;
            this.appRoot = appRoot;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

            this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            try
            {
                this.eventSource.Message("Starting web server on " + this.listeningAddress);

                this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

                this.eventSource.Message("Listening on " + this.publishAddress);

                return Task.FromResult(this.publishAddress);
            }
            catch (Exception ex)
            {
                this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

                this.StopWebServer();

                throw;
            }
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
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

Maintenant que tous les composants sont en place, votre projet doit ressembler à une application API Web type avec des points d’entrée d’API Reliable Services et un hôte OWIN :

![API Web avec des points d’entrée d’API Reliable Services et un hôte OWIN](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Exécuter et se connecter via un navigateur web
Si ce n'est déjà fait, [configurez votre environnement de développement](service-fabric-get-started.md).

Vous pouvez désormais générer et déployer votre service. Appuyez sur **F5** dans Visual Studio pour générer et déployer l'application. Dans la fenêtre Événements de diagnostic, un message doit apparaître et indiquer que le serveur web s’est ouvert sur http://localhost:8281/.

![Fenêtre Événements de diagnostic de Visual Studio](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [!NOTE]
> Si le port a déjà été ouvert par un autre processus sur votre ordinateur, une erreur risque d’apparaître ici. Elle indique que l’écouteur n’a pas pu être ouvert. Dans ce cas, essayez d’utiliser un autre port pour la configuration du point de terminaison dans le fichier ServiceManifest.xml.
> 
> 

Une fois le service en cours d’exécution, ouvrez un navigateur et accédez à [http://localhost:8281/api/values](http://localhost:8281/api/values) pour le tester.

## Montée en charge
La montée en charge d’applications web sans état implique généralement l’ajout d’ordinateurs supplémentaires sur lesquels les applications web seront exécutées. Le moteur d'orchestration de Service Fabric peut réaliser cette opération chaque fois que de nouveaux nœuds sont ajoutés à un cluster. Quand vous créez des instances d’un service sans état, vous pouvez spécifier le nombre d’instances à générer. Service Fabric place ce nombre d’instances sur les nœuds du cluster, en veillant à ne pas créer plusieurs instances sur un même nœud. Vous pouvez également demander à Service Fabric de toujours créer une instance sur chaque nœud en spécifiant **-1** comme nombre d’instances. Cela garantit que chaque fois que vous ajoutez des nœuds pour faire évoluer votre cluster, une instance de votre service sans état sera créée sur les nouveaux nœuds. Cette valeur est une propriété de l’instance de service, elle est donc définie quand vous créez une instance de service. Pour ce faire, vous pouvez utiliser PowerShell :

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Vous pouvez également le faire quand vous définissez un service par défaut dans un projet de service sans état Visual Studio :

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Pour plus d’informations sur la façon de créer des instances d’application et de service, consultez [Déployer une application](service-fabric-deploy-remove-applications.md).

## Étapes suivantes
[Débogage de votre application Service Fabric à l’aide de Visual Studio](service-fabric-debugging-your-application.md)

<!---HONumber=AcomDC_0914_2016-->