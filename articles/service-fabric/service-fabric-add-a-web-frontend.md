<properties
   pageTitle="Créer un serveur web frontal pour votre application | Microsoft Azure"
   description="Exposer votre application Service Fabric sur le web à l’aide d’un projet d’API web ASP.NET 5 et de la communication interservice via ServiceProxy."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/21/2015"
   ms.author="seanmck"/>


# Créer un service web frontal pour votre application

Par défaut, les services Service Fabric ne fournissent pas une interface publique sur le web. Pour exposer les fonctionnalités de votre application pour les clients HTTP, vous devez créer un projet web qui agit comme point d’entrée et ensuite établir la communication avec vos services individuels.

Dans ce didacticiel, nous allons vous guider tout au long de l'étape d'ajout d'une API web ASP.NET 5 frontale à une application qui comprend déjà un service fiable (Reliable Service) basé sur le modèle du projet de service avec état. Si vous ne l’avez pas déjà fait, passez en revue les étapes de la procédure [Création de votre première application dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) avant de commencer ce didacticiel.


## Ajouter un service ASP.NET 5 à votre application

ASP.NET 5 est une infrastructure légère de développement web inter-plateformes, permettant la création de d’API web et d’interfaces utilisateur web modernes. Ajoutons un projet d’API web ASP.NET à notre application existante.

1. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Services** dans le projet d'application et choisissez **Ajouter Fabric Service**.

	![Ajout d’un nouveau service à une application existante][vs-add-new-service]

2. Dans la boîte de dialogue Nouveau service, choisissez **ASP.NET 5** et donnez-lui un nom.

	![Choix du service web ASP.NET dans la boîte de dialogue Nouveau service][vs-new-service-dialog]

3. La boîte de dialogue suivante fournit un ensemble de modèles de projets ASP.NET 5. Notez que ce sont ces mêmes modèles que vous verriez si vous aviez créé un projet ASP.NET 5 en dehors d’une application Service Fabric. Pour ce didacticiel, nous allons choisir l'**API web**, mais vous pouvez appliquer les mêmes concepts pour la création d'une application web complète.

	![Choix du type de projet ASP.NET][vs-new-aspnet-project-dialog]

    Une fois votre projet d’API web créé, vous aurez deux services dans votre application. Lorsque vous continuez à générer votre application, vous allez ajouter davantage de services exactement de la même façon et chacun peut être mis à niveau et faire l’objet d’un contrôle de version indépendamment.


## Exécution de l'application

Pour obtenir une idée de ce que nous avons fait, nous allons déployer la nouvelle application et observer le comportement par défaut fourni par le modèle d’API web ASP.NET 5.

1. Appuyez sur F5 dans Visual Studio pour déboguer l’application.

2. Une fois le déploiement terminé, Visual Studio lance le navigateur à la racine du service API web ASP.NET, avec une syntaxe similaire à http://localhost:33003 (le numéro de port est attribué de manière aléatoire et peut être différent sur votre ordinateur). Le modèle d’API web ASP.NET 5 ne fournit pas de comportement par défaut pour la racine, donc vous obtiendrez une erreur dans le navigateur.

3. Ajoutez `/api/values` à l'emplacement dans le navigateur. Cette action ouvre la méthode `Get` sur le ValuesController dans le modèle d'API web et retourne la réponse par défaut fournie par le modèle, un tableau JSON contenant deux chaînes :

    ![Valeurs par défaut retournées par le modèle d’API web ASP.NET 5][browser-aspnet-template-values]

    À la fin du didacticiel, nous aurons remplacé ces valeurs par défaut par la valeur de compteur la plus récente de notre service avec état.


## Connecter les services

Service Fabric fournit une flexibilité complète sur votre façon de communiquer avec Reliable Services. Dans une application unique, vous pouvez avoir des services accessibles via TCP, d’autres via une API REST HTTP et d’autres encore via des sockets web. Pour des informations sur les options disponibles et les compromis inhérents, consultez [Communication avec les services](service-fabric-connect-and-communicate-with-services.md). Dans ce didacticiel, nous suivrons l’une des approches les plus simples et utiliserons les classes `ServiceProxy`/`ServiceRemotingListener` fournies dans le Kit de développement logiciel (SDK).

Dans l'approche `ServiceProxy` (modélisée sur les appels de procédure distante ou RPC), vous définissez une interface qui agit comme contrat public pour le service, puis utilisez cette interface pour générer une classe proxy pour l'interaction avec le service.


### Créer l’interface

Nous allons commencer par la création de l’interface en tant que contrat entre le service avec état et ses clients, y compris le projet ASP.NET 5.

1. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur votre solution et choisissez **Ajouter > Nouveau projet**.

2. Choisissez l'entrée Visual C# dans le volet de navigation gauche, puis sélectionnez le modèle **Bibliothèque de classes**. Assurez-vous que la version de .NET Framework est définie sur 4.5.1.

    ![Création d’un projet d’interface de votre service avec état][vs-add-class-library-project]

3. Pour qu'une interface soit utilisable par `ServiceProxy`, elle doit dériver de l'interface IService, incluse dans l'un des packages NuGet Service Fabric. Pour ajouter le package, cliquez avec le bouton droit sur votre projet de bibliothèque de classes et choisissez **Gérer les packages NuGet**.

4. Vérifiez que la case **Inclure la version préliminaire** est cochée, puis recherchez le package **Microsoft.ServiceFabric.Services** et installez-le.

    ![Obtention du package NuGet de services][vs-services-nuget-package]

5. Dans la bibliothèque de classes, créez une interface avec une méthode unique, `GetCountAsync`, et étendez l'interface depuis IService.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### Mettre en œuvre l’interface dans votre service avec état

Maintenant que nous avons défini l’interface, nous devons la mettre en œuvre dans notre service avec état.

1. Dans votre service avec état, ajoutez une référence au projet de la bibliothèque de classes contenant l’interface.

    ![Ajout d’une référence au projet de bibliothèque de classes dans le service avec état][vs-add-class-library-reference]

2. Recherchez la classe qui hérite de `StatefulService`, par exemple `MyStatefulService`, et étendez-la pour mettre en œuvre l'interface `ICounter`.

    ```c#
    using MyStatefulService.Interfaces;

    ...

    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. À présent, mettez en œuvre la méthode unique définie par l'interface `ICounter`, `GetCountAsync`.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### Exposer le service avec état à l’aide de ServiceRemotingListener

Avec l'interface `ICounter` mise en œuvre, l'étape finale de l'activation du service avec état à appeler à partir d'autres services consiste à ouvrir un canal de communication. Pour les services avec état, Service Fabric fournit une méthode remplaçable appelée `CreateServiceReplicaListeners` où vous pouvez spécifier un ou plusieurs écouteurs de communication basés sur le type de communication à activer pour votre service.

>[AZURE.NOTE]La méthode équivalente pour ouvrir un canal de communication sur des services sans état est appelée `CreateServiceInstanceListeners`.

Dans ce cas, nous remplaçons la méthode `CreateServiceReplicaListeners` existante et vous proposons un `ServiceRemotingListener`, ce qui crée un point de terminaison RPC pouvant être appelé à partir de clients avec `ServiceProxy`.

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (initParams) =>
                new ServiceRemotingListener<ICounter>(initParams, this))
    };
}
```


### Utiliser le ServiceProxy pour interagir avec le service

Notre service avec état est maintenant prêt à recevoir le trafic provenant d’autres services de sorte qu’il reste uniquement à ajouter le code pour établir la communication à partir du service web ASP.NET.

1. Dans votre projet ASP.NET, ajoutez une référence à la bibliothèque de classes contenant l’interface `ICounter`.

2. Ajoutez le package Microsoft.ServiceFabric.Services au projet ASP.NET, tout comme vous l’avez fait précédemment pour le projet de bibliothèque de classes. Ceci fournira la classe `ServiceProxy`.

3. Dans le dossier des contrôleurs, ouvrez la classe `ValuesController`. Notez que la méthode `Get` renvoie actuellement uniquement un tableau de chaînes codées en dur avec « valeur1 » et « valeur2 », ce qui correspond à ce que nous avons vu précédemment dans le navigateur. Remplacez par le code suivant :

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(0, new Uri("fabric:/MyApp/MyStatefulService"));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    La première ligne de code est la clé. Pour créer le proxy ICounter sur le service avec état, vous devez fournir deux informations : un ID de partition et le nom du service.

    Le partitionnement vous permet de mettre à l’échelle des services avec état en les fractionnant dans différents groupes en fonction de la clé définie, comme l’ID client ou le code postal. Dans notre exemple d'application, le service avec état ne dispose que d'une seule partition, donc la clé importe peu : n'importe quelle clé fournie aboutira à la même partition. Pour en savoir plus sur le partitionnement des services, consultez [Comment partitionner les services fiables (Reliable Services) de Service Fabric](service-fabric-concepts-partitioning).

    Le nom du service est un URI de la structure de formulaire :/&lt;nom\_application&gt;/&lt;nom\_service&gt

    Avec ces deux éléments d’information, Service Fabric peut identifier de façon unique l’ordinateur auquel les demandes doivent être envoyées. Le `ServiceProxy` gère également de façon transparente le cas où l'ordinateur qui héberge la partition de notre service avec état échoue et où vous devez promouvoir un autre ordinateur pour prendre sa place. Cette abstraction facilite de manière significative l’écriture du code client pour la gestion avec d’autres services.

    Une fois que nous avons le proxy, nous appelons simplement la méthode `GetCountAsync` et retournons son résultat.

4. Appuyez sur F5 de nouveau pour exécuter l’application modifiée. Comme précédemment, Visual Studio lance automatiquement le navigateur à la racine du projet web. Ajoutez le chemin « api/values » et vous devriez voir la valeur actuelle du compteur retournée.

    ![La valeur du compteur avec état affichée dans le navigateur][browser-aspnet-counter-value]

    Actualisez le navigateur régulièrement afin de vérifier la mise à jour de la valeur du compteur.


## Qu’en est-il des acteurs ?

Ce didacticiel s’est concentré sur l’ajout d’un serveur web frontal communiquant avec un service avec état, mais vous pouvez suivre un modèle très similaire pour communiquer avec les acteurs. En fait, il est un peu plus simple.

Lorsque vous créez un projet d’acteur, Visual Studio génère automatiquement un projet d’interface pour vous. Vous pouvez utiliser cette interface pour générer un proxy d’acteur intervenant dans le projet web pour communiquer avec l’acteur. Le canal de communication est fourni automatiquement, vous n’avez donc aucune opération à faire, telle que l’établissement d’un `ServiceRemotingListener` comme vous l’avez fait pour le service avec état dans ce didacticiel.

## Services web en cours d’exécution sur un cluster local

En général, vous pouvez déployer exactement la même application Service Fabric sur un cluster de plusieurs ordinateurs déployé sur votre cluster local et être assuré qu’elle fonctionnera comme prévu, étant donné que votre cluster local est simplement une configuration de cinq nœuds réduite à un seul ordinateur.

En ce qui concerne les services web, toutefois, il existe une nuance clé. Lorsque votre cluster se trouve derrière un équilibreur de charge, comme c’est le cas dans Azure, vous devez vous assurer que vos services web sont déployés sur chaque ordinateur, car l’équilibreur de charge alternera simplement le trafic entre les ordinateurs. Cela est possible en définissant `InstanceCount` pour le service sur la valeur spéciale de -1. En revanche, lorsque vous exécutez le système en local, vous devez vous assurer que seule une instance du service est en cours d’exécution ; sinon, vous constaterez des conflits depuis plusieurs processus écoutant sur les mêmes chemin et port. Par conséquent, le nombre d'instances de service web doit être défini sur 1 pour les déploiements locaux.

Pour apprendre à configurer des valeurs différentes pour un environnement différent, consultez [Gestion des paramètres d'application pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md).

## Étapes suivantes

- [Créer un cluster dans Azure en déployant votre application dans le cloud](service-fabric-cluster-creation-via-portal.md)
- [En savoir plus sur la communication avec les services](service-fabric-connect-and-communicate-with-services.md)
- [En savoir plus sur le partitionnement des services avec état](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png

<!---HONumber=AcomDC_0107_2016-->