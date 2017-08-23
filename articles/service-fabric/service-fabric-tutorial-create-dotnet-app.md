---
title: "Créer une application .NET pour Service Fabric | Microsoft Docs"
description: "Découvrez comment créer une application avec un service frontal ASP.NET Core et un service principal fiable avec état, et déployer l’application sur un cluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi, mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 71d910bc0e459528805521ba991e5291396a3b8d
ms.contentlocale: fr-fr
ms.lasthandoff: 08/10/2017

---

# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Créer et déployer une application avec un service frontal API Web ASP.NET Core et un service principal avec état
Premier d’une série, ce didacticiel vous montre comment créer une application Azure Service Fabric avec un service frontal API Web ASP.NET Core, et un service principal avec état pour stocker vos données. 

![Diagramme de l’application](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Dans ce premier volet, vous apprenez à :

> [!div class="checklist"]
> * Créer un service API Web ASP.NET Core en tant que service fiable
> * Créer un service fiable avec état
> * Implémenter la communication à distance des services et l’utilisation d’un proxy de service

Cette série de didacticiels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer une application .NET Service Fabric
> * [Déployer l’application sur un cluster distant](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Configurer l’intégration et le déploiement continus à l’aide de Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

## <a name="prerequisites"></a>Prérequis
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Installez Visual Studio 2017](https://www.visualstudio.com/) et les charges de travail **Développement Azure** et **Développement web et ASP.NET**.
- [Installez le kit SDK Service Fabric](service-fabric-get-started.md).

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Créer un service API Web ASP.NET en tant que service fiable
ASP.NET Core est une infrastructure légère de développement web inter-plateformes, que vous pouvez utiliser pour créer des API web et d’interfaces utilisateur web modernes. Pour bien comprendre comment ASP.NET Core s’intègre avec Service Fabric, il est vivement recommandé de lire l’article [ASP.NET Core dans le modèle Reliable Services de Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md). Pour l’instant, vous pouvez suivre ce didacticiel pour démarrer rapidement. Pour en savoir plus sur ASP.NET Core, consultez la [Documentation d’ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

> [!NOTE]
> Ce didacticiel est basé sur les [outils ASP.NET Core pour Visual Studio 2017](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc). Les outils .NET Core pour Visual Studio 2015 ne sont plus mis à jour.

1. Lancez Visual Studio en tant qu’**administrateur**.

2. Créez un projet en sélectionnant **Fichier**->**Nouveau**->**Projet**.

3. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Cloud > Application Service Fabric**.

4. Appelez l’application **MonApplication**, puis cliquez sur **OK**.

   ![Boîte de dialogue Nouveau projet dans Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Dans la page **Nouveau service Service Fabric**, choisissez **ASP.NET Core sans état** et nommez votre service **MyWebAPIFrontEnd**.
   
   ![Choix du service web ASP.NET dans la boîte de dialogue Nouveau service](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. La page suivante fournit un ensemble de modèles de projets ASP.NET Core. Pour ce didacticiel, sélectionnez **API web**. Toutefois, vous pouvez appliquer les mêmes concepts pour la création d'une application web complète.
   
   ![Choix du type de projet ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio crée une application et un projet de service, et les affiche dans l’Explorateur de solutions.

   ![Explorateur de solutions après la création de l’application avec un service API Web ASP.NET Core]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="deploy-and-debug-the-application-locally"></a>Déployer et déboguer l’application localement
Vous pouvez maintenant déboguer l’application et examiner le comportement par défaut fourni par le modèle API Web ASP.NET Core.

Appuyez sur `F5` dans Visual Studio pour déployer l’application en vue d’un débogage. La commande `F5` échoue si vous n’avez pas précédemment ouvert Visual Studio en tant qu’**administrateur**.

> [!NOTE]
> La première fois que vous exécutez et déployez l’application localement, Visual Studio crée un cluster local pour le débogage, ce qui peut prendre un certain temps. L’état de la création du cluster est affiché dans la fenêtre Sortie de Visual Studio.

Une fois le cluster prêt, vous obtenez une notification de la part de l’application de gestionnaire de cluster local Service Fabric dans la barre d’état système.

1. Pour déboguer l’application, appuyez sur F5 dans Visual Studio.
2. À la fin du déploiement, Visual Studio lance le navigateur à la racine du service API Web ASP.NET. Comme le modèle API Web ASP.NET Core ne fournit pas de comportement par défaut pour la racine, vous obtenez une erreur dans le navigateur.
3. Ajoutez `/api/values` à l’URL dans le navigateur. Cette requête appelle la méthode `Get` sur ValuesController dans le modèle API Web. Elle retourne la réponse par défaut fournie par le modèle, un tableau JSON contenant deux chaînes :
   
![Valeurs par défaut retournées par le modèle d’API web ASP.NET Core](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-template-values.png)

> [!NOTE]
> Pour modifier le comportement par défaut de Visual Studio 2017 lors du débogage d’une application, vous pouvez modifier les propriétés du projet d’application Service Fabric **MyApplication**.
> Pour ce didacticiel, la définition du mode de débogage d’application sur **Actualiser l’application** et l’ajout de **’/api/values’** à la propriété URL de l’application permet d’optimiser l’expérience de débogage.
> 

Pour arrêter le débogage de l’application, revenez à Visual Studio et appuyez sur **Maj + F5**.

### <a name="understanding-the-service-fabric-application-and-service"></a>Présentation de l’application et du service Service Fabric
Votre solution Visual Studio contient maintenant deux projets.
1. Le projet d’application Service Fabric : **MyApplication**
    - Ce projet ne contient pas directement de code. Au lieu de cela, il fait référence à un ensemble de projets de service. Par ailleurs, il inclut d’autres types de contenu pour spécifier comment votre application est composée et déployée.
2. Le projet de service : **MyWebAPIFrontEnd**
    - Il s’agit de votre projet d’API Web ASP.NET Core, qui contient le code et la configuration de votre service. Si vous examinez le fichier de code ValuesController.cs dans le dossier Contrôleurs, vous pouvez remarquer qu’il s’agit d’un contrôleur d’API Web ASP.NET Core normal. Le code que vous écrivez dans le cadre de vos contrôleurs ne fait l’objet d’aucune exigence particulière pour l’exécution d’une API Web ASP.NET Core en tant que service fiable dans Service Fabric.

Pour plus d’informations sur le modèle d’application dans Service Fabric, consultez [Modéliser une application dans Service Fabric](service-fabric-application-model.md).

Pour plus d’informations sur le contenu du projet de service, consultez [Prise en main de Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Ajouter un service principal avec état à votre application
Maintenant qu’un service API Web ASP.NET est exécuté dans notre application, ajoutons un service fiable avec état pour stocker des données dans notre application.

Service Fabric permet de stocker de manière cohérente et fiable vos données directement dans votre service à l’aide de collections fiables. Les collections fiables sont un ensemble simple de classes de collections hautement disponibles et fiables avec lesquelles tous ceux qui ont utilisé les collections C# sont familiers.

Dans ce didacticiel, vous créez un service, qui stocke une valeur de compteur dans une collection fiable.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Services** dans le projet d’application et choisissez **Ajouter > Nouveau Service Fabric**.
   
    ![Ajout d’un nouveau service à une application existante](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. Dans la boîte de dialogue **Nouveau service Service Fabric**, choisissez **Service avec état**, nommez le service **MyStatefulService**, puis appuyez sur **OK**.

    ![Boîte de dialogue Nouveau service dans Visual Studio.](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    Une fois votre projet de service créé, votre application contient deux services. À mesure que vous continuez à développer votre application, vous pouvez ajouter d’autres services de la même façon. Chacun peut être mis à niveau et faire l'objet d'un contrôle de version indépendamment.

### <a name="deploy-and-debug-the-application-locally"></a>Déployer et déboguer l’application localement
Maintenant que le nouveau service a été ajouté à l’application, déboguons l’application complète et examinons le comportement par défaut du nouveau service.

Pour déboguer l’application, appuyez sur F5 dans Visual Studio.

> [!NOTE]
> Si vous choisissez d’utiliser **Actualiser l’application** comme mode de débogage de l’application, vous êtes invité à autoriser le cluster Service Fabric local à accéder au dossier de sortie de la génération de votre application.
> 

Les deux services de votre application sont générés, déployés et déclarés dans votre cluster Service Fabric local. Une fois que les services ont démarré, Visual Studio ouvre automatiquement la visionneuse **Événements de diagnostic**, où vous pouvez observer la sortie de trace de vos services.
   
![Observateur d’événements de diagnostic](./media/service-fabric-tutorial-create-dotnet-app/diagnostic-events-viewer.png)

La visionneuse Événements de diagnostic affiche les messages de trace de tous les services qui font partie de la solution Visual Studio en cours de débogage. En mettant en pause la visionneuse Événements de diagnostic, vous pouvez développer l’un des messages de service afin d’examiner ses propriétés. Vous pouvez ainsi voir quel service du cluster a émis le message, le nœud sur lequel l’instance de service est en cours d’exécution et d’autres informations.

![Observateur d’événements de diagnostic](./media/service-fabric-tutorial-create-dotnet-app/expanded-diagnostics-viewer.png)

Comme la propriété **serviceTypeName** présente la valeur **MyStatefulServiceType**, vous pouvez constater que les messages de service proviennent du service avec état que nous venons de créer. Vous pouvez également observer que les messages envoyés présentent le texte « Current Counter Value: » (Valeur actuelle du compteur :). Ce message est émis par la ligne de code de la méthode `RunAsync` du fichier **MyStatefulService.cs** encadrée dans la capture d’écran suivante.

![Code du message de service](./media/service-fabric-tutorial-create-dotnet-app/service-message-code.png)

Pour plus d’informations sur l’émission d’informations de diagnostic à partir de vos services et applications, consultez [Surveillance et diagnostics pour Azure Service Fabric](service-fabric-diagnostics-overview.md).

Pour arrêter le débogage de l’application, revenez à Visual Studio et appuyez sur **Maj + F5**.

### <a name="understanding-the-mystatefulservice-code"></a>Explication du code de MyStatefulService
Pour comprendre comment un dictionnaire fiable est utilisé pour stocker les données dans le cluster, prenons quelques minutes pour examiner le code dans le service **MyStatefulService**.

Le service contient cinq lignes de code liées à la création, à la mise à jour et à la lecture du dictionnaire fiable.

![Code du dictionnaire fiable](./media/service-fabric-tutorial-create-dotnet-app/reliable-dictionary-code.png)

1. À chaque fois que la méthode `RunAsync` est exécutée (ce qui se produit au démarrage du service), cette ligne de code obtient ou ajoute au service un dictionnaire fiable nommé `myDictionary`.
2. Toute interaction avec les valeurs d’un dictionnaire fiable requiert une transaction. Cette instruction using entrée dans cette ligne de code crée cette transaction.
3. Cette ligne de code obtient la valeur associée à la clé spécifiée dans l’appel de méthode, par exemple, `Counter`.
4. Cette ligne de code met à jour la valeur associée à la clé `Counter` en incrémentant cette valeur.
5. Cet appel de méthode valide la transaction et renvoie le résultat une fois que la valeur mise à jour est stockée sur un quorum de nœuds de votre cluster.

Pour plus d’informations sur les dictionnaires fiables et les collections fiables, consultez [Introduction aux Collections fiables dans les services avec état d’Azure Service Fabric](service-fabric-reliable-services-reliable-collections.md).

## <a name="connect-the-services"></a>Connecter les services
Dans l’étape qui suit, nous allons connecter les deux services et faire en sorte que l’API Web frontale renvoie la valeur actuelle à partir du dictionnaire fiable du service principal.

Service Fabric fournit une flexibilité complète sur votre façon de communiquer avec Reliable Services. Dans une même application, vous pouvez avoir des services qui sont accessibles via TCP, d’autres services accessibles via une API REST HTTP et encore d’autres services accessibles via des sockets web. Pour obtenir des informations sur les options disponibles et leurs avantages/inconvénients respectifs, consultez [Communication avec les services](service-fabric-connect-and-communicate-with-services.md).

Dans ce didacticiel, nous utilisons la [communication à distance des services avec Reliable Services](service-fabric-reliable-services-communication-remoting.md).

Dans l’approche de communication à distance des services (modélisée sur les appels de procédure distante ou RPC), vous définissez une interface qui jouera le rôle de contrat public pour le service. Ensuite, vous utilisez cette interface pour générer une classe proxy pour l'interaction avec le service.

### <a name="create-the-remoting-interface"></a>Créer l’interface de communication à distance
Nous commençons par créer l’interface qui jouera le rôle de contrat entre les deux services. L’interface devant être référencée par tous les services qui l’utilisent, nous la créons dans un projet de bibliothèque de classes distinct.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre solution et choisissez **Ajouter** > **Nouveau projet**.
2. Choisissez l’entrée **Visual C#** dans le volet de navigation gauche, puis sélectionnez le modèle **Bibliothèque de classes (.NET Framework)**. Assurez-vous que la version du .NET Framework est définie sur **4.5.2** ou une version supérieure.
   
    ![Création d’un projet d’interface de votre service avec état](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-project.png)

3. Nommez la bibliothèque de classes **MyStatefulService.Interface** et cliquez sur **OK**.

4. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution**.

5. Choisissez **Parcourir** et recherchez le package **Microsoft.ServiceFabric.Services.Remoting**. Choisissez de l’installer pour les trois projets de service de la solution : 
   
    ![Obtention du package NuGet de services](./media/service-fabric-tutorial-create-dotnet-app/add-nuget.png)

6. Dans la bibliothèque de classes, créez une interface avec une méthode unique, `GetCountAsync`, et étendez l'interface depuis `Microsoft.ServiceFabric.Services.Remoting.IService`. L’interface de communication à distance doit dériver de cette interface pour indiquer qu’il s’agit d’une interface de service à distance. 
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;  
    using System.Threading.Tasks;
    ...
    namespace MyStatefulService.Interface
    {           
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```
7. Cliquez avec le bouton droit sur le projet **MyStatefulService.Interface** dans l’Explorateur de solutions et sélectionnez **Propriétés**. Sélectionnez l’onglet **Build**, puis sélectionnez la valeur **x64** dans la liste déroulante **Plateforme cible**. 

8. Enregistrez toutes vos modifications.

### <a name="implement-the-interface-in-your-stateful-service"></a>Mettre en œuvre l’interface dans votre service avec état
Maintenant que nous avons défini l'interface, nous devons la mettre en œuvre dans notre service avec état.

1. À partir du projet **MyStatefulService**, ajoutez une référence au projet de bibliothèque de classes contenant l’interface.
   
    ![Ajout d’une référence au projet de bibliothèque de classes dans le service avec état](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference.png)

    ![Ajout d’une référence au projet de bibliothèque de classes dans le service avec état](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference-2.png)

2. Ouvrez le fichier `MyStatefulService.cs` de fichier et étendez-le pour implémenter l’interface `ICounter` que nous avons créée.
   
    ```c#
    using MyStatefulService.Interface;
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. À présent, implémentez la méthode unique définie par l’interface `ICounter`, `GetCountAsync`.
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary =
          await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

    - Cette méthode renvoie la valeur stockée de la clé `Counter` dans le dictionnaire fiable nommé `myDictionary`. 

### <a name="expose-the-stateful-service-using-service-remoting"></a>Exposer le service avec état à l’aide de la communication à distance des services
Une fois l’interface `ICounter` implémentée, l’étape finale consiste à ouvrir le point de terminaison de communication à distance des services. Pour les services avec état, Service Fabric fournit une méthode remplaçable appelée `CreateServiceReplicaListeners`. Avec cette méthode, vous pouvez spécifier un ou plusieurs écouteurs de communication, en fonction du type de communication que vous voulez activer pour votre service.

Dans ce cas, nous remplaçons la méthode `CreateServiceReplicaListeners` existante par une instance de `ServiceRemotingListener`, ce qui crée un point de terminaison RPC pouvant être appelé à partir de clients via `ServiceProxy`.  

Modifiez la méthode **CreateServiceReplicaListeners** dans le fichier **MyStatefulService.cs** et ajoutez une instruction using à l’espace de noms `Microsoft.ServiceFabric.Services.Remoting.Runtime`.

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```

Notre service avec état est maintenant prêt à recevoir le trafic provenant d’autres services via RPC à l’aide de la communication à distance des services.

### <a name="call-the-stateful-back-end-service-from-the-front-end-service"></a>Appeler le service principal avec état à partir du service frontal
Maintenant que notre service principal a exposé une interface, il ne nous reste plus qu’à ajouter le code pour communiquer avec celui-ci à partir du service API Web ASP.NET. Pour communiquer à l’aide de la communication à distance des services, nous utilisons un proxy de service à partir de notre contrôleur Values.

1. Dans l’Explorateur de solutions, développez **MyWebAPIFrontEnd**, cliquez avec le bouton droit sur **Dépendances**, puis sélectionnez **Ajouter une référence**.  Sélectionnez **MyStatefulService.Interface** et cliquez sur **OK**.
   
2. Dans le dossier **Contrôleurs**, ouvrez le fichier `ValuesController.cs`. Ajoutez les instructions using suivantes au fichier :

    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
    ```
    
3. Actuellement, la méthode `Get` renvoie uniquement un tableau de chaînes codées en dur avec « valeur1 » et « valeur2 », ce qui correspond à ce que nous avons vu précédemment dans le navigateur. Remplacez par le code suivant :
   
    ```
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
 
    La première ligne de code dans cette méthode crée l’objet ServiceProxy dans le service avec état à l’aide de l’interface ICounter. Lorsque vous créez un objet ServiceProxy, vous devez fournir deux informations : un ID de partition et le nom du service.
   
    Les services avec état peuvent être partitionnés à l’échelle en répartissant les données dans différents compartiments en fonction d’une clé, par exemple un ID client ou un code postal. Dans notre exemple d'application, le service avec état ne dispose que d'une seule partition, donc la clé importe peu. N’importe quelle clé fournie aboutit à la même partition. Pour en savoir plus sur le partitionnement des services, consultez [Comment partitionner les services fiables (Reliable Services) de Service Fabric](service-fabric-concepts-partitioning.md).
   
    Le nom du service est un URI de la forme : fabric:/&lt;nom_application&gt;/&lt;nom_service&gt;.
   
    Avec ces deux éléments d'information, Service Fabric peut identifier de façon unique l'ordinateur auquel les demandes doivent être envoyées. La classe `ServiceProxy` traite également sans problème le cas dans lequel la machine qui héberge la partition de service avec état échoue et une autre machine est promue pour prendre sa place. Cette abstraction facilite l’écriture du code client pour la gestion d’autres services.
   
    Une fois que nous avons le proxy, nous appelons la méthode `GetCountAsync` et renvoyons son résultat.

4. Appuyez sur F5 de nouveau pour exécuter l'application modifiée. Comme précédemment, Visual Studio lance automatiquement le navigateur à la racine du projet web. Ajoutez le chemin « api/values » et vous devriez voir la valeur actuelle du compteur retournée.
   
    ![La valeur du compteur avec état affichée dans le navigateur](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-counter-value.png)
   
    Actualisez le navigateur régulièrement afin de vérifier la mise à jour de la valeur du compteur.

Pour arrêter le débogage de l’application, revenez à Visual Studio et appuyez sur **Maj + F5**.

## <a name="next-steps"></a>Étapes suivantes
Dans cette partie du didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un service API Web ASP.NET Core en tant que service fiable
> * Créer un service fiable avec état
> * Implémenter la communication à distance des services et l’utilisation d’un proxy de service

Passez au didacticiel suivant :
> [!div class="nextstepaction"]
> [Déploiement de l’application dans Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
