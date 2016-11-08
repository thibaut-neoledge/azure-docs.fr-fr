---
title: Prise en main de Reliable Services | Microsoft Docs
description: Introduction à la création d'une application Microsoft Azure Service Fabric avec des services avec et sans état.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2016
ms.author: vturecek

---
# Prise en main de Service Fabric Reliable Services
Une application Azure Service Fabric contient un ou plusieurs services qui exécutent votre code. Ce guide vous montre comment créer des applications Service Fabric avec et sans état à l’aide de [Reliable Services](service-fabric-reliable-services-introduction.md).

## Création d'un service sans état
Un service sans état est un type de service qui correspond actuellement à la norme pour les applications cloud. Le service lui-même est considéré comme étant sans état, car il ne contient pas de données à stocker de manière fiable ou à rendre hautement disponibles. Si une instance d’un service sans état est arrêtée, tout son état interne est perdu. Dans ce type de service, l’état doit être conservé dans un magasin externe, comme des tables Azure ou une base de données SQL, pour être hautement disponible et fiable.

Lancez Visual Studio 2015 en tant qu’administrateur et créez un projet d’application Service Fabric nommé *HelloWorld* :

![Utiliser la boîte de dialogue Nouveau projet pour créer une application Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Créez ensuite un projet de service sans état nommé *HelloWorldStateless* :

![Dans la deuxième boîte de dialogue, créer un projet de service sans état](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Votre solution contient maintenant deux projets :

* *HelloWorld*. Il s’agit du projet d’*application* qui contient vos *services*. Il contient également le manifeste de l’application qui décrit l’application, ainsi que plusieurs scripts PowerShell qui vous aident à déployer votre application.
* *HelloWorldStateless*. Il s’agit du projet de service. Il contient l’implémentation du service sans état.

## Mettre en œuvre le service
Ouvrez le fichier **HelloWorldStateless.cs** dans le projet de service. Dans Service Fabric, un service peut exécuter n’importe quelle logique métier. L'API de service fournit deux points d'entrée pour votre code :

* Une méthode de point d’entrée de durée indéterminée appelée *RunAsync* avec laquelle vous pouvez commencer l’exécution de toute charge de travail, y compris les charges de travail de calcul de longue durée.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Un point d’entrée de communication où vous pouvez connecter votre pile de communication, notamment l’API Web ASP.NET : C’est là que vous pouvez commencer à recevoir des demandes des utilisateurs et des autres services.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Dans ce didacticiel, nous allons nous concentrer sur la méthode de point d’entrée `RunAsync()`. C’est là que vous pouvez commencer immédiatement à exécuter votre code. Le modèle de projet inclut un exemple d’implémentation de `RunAsync()` qui incrémente un comptage continu.

> [!NOTE]
> Pour plus d’informations sur l’utilisation d’une pile de communication, consultez [Services de l’API Web Service Fabric avec auto-hébergement OWIN](service-fabric-reliable-services-communication-webapi.md).
> 
> 

### RunAsync
```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

La plateforme appelle cette méthode quand une instance d’un service est placée et prête à être exécutée. Pour un service sans état, cela signifie simplement le moment où l’instance de service est ouverte. Un jeton d'annulation est fourni pour coordonner lorsque l'instance de service doit être fermée. Dans Service Fabric, ce cycle d’ouverture/fermeture d’une instance de service peut se produire plusieurs fois au cours de la durée de vie de votre service dans son ensemble. Il existe diverses raisons à cela, notamment :

* Le système déplace vos instances de service à des fins d’équilibrage des ressources.
* Des erreurs surviennent dans votre code.
* L’application ou le système sont mis à niveau.
* Le matériel sous-jacent tombe en panne.

Cette orchestration est gérée par le système afin de maintenir une haute disponibilité et un équilibrage correct de votre service.

`RunAsync()` est exécuté dans sa propre tâche. Notez que dans l’extrait de code ci-dessus, nous sommes directement passés à une boucle *while*. Il est inutile de planifier une tâche distincte pour votre charge de travail. L'annulation de votre charge de travail est un effort conjoint orchestré par le jeton d'annulation fourni. Le système attend la fin de la tâche (suite à sa réussite, son annulation ou sa défaillance) avant de poursuivre. Il est important de respecter le jeton d’annulation, de terminer le travail et de quitter `RunAsync()` aussi rapidement que possible quand le système demande une annulation.

Dans cet exemple de service sans état, le décompte est stocké dans une variable locale. Mais comme il s’agit d’un service sans état, la valeur stockée existe uniquement pendant le cycle de vie actuel de son instance de service. Lorsque le service se déplace ou redémarre, la valeur est perdue.

## Création d'un service avec état
Service Fabric introduit un nouveau type de service qui est avec état. Un service avec état peut conserver un état de façon fiable dans le service lui-même, colocalisé avec le code qui l’utilise. L’état est rendu hautement disponible par Service Fabric sans avoir besoin de le conserver dans un magasin externe.

Pour convertir une valeur de compteur sans état en valeur hautement disponible et persistante, même quand le service se déplace ou redémarre, vous avez besoin d’un service avec état.

Dans la même application *HelloWorld*, vous pouvez ajouter un nouveau service en cliquant avec le bouton droit sur les références des Services dans le projet d’application et en sélectionnant **Ajouter -> Nouveau service Service Fabric**.

![Ajouter un service à votre application Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Sélectionnez **Service avec état** et nommez-le *HelloWorldStateful*. Cliquez sur **OK**.

![Utiliser la boîte de dialogue Nouveau projet pour créer un service avec état Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Votre application doit maintenant contenir deux services : le service sans état *HelloWorldStateless* et le service avec état *HelloWorldStateful*.

Un service avec état a les mêmes points d'entrée qu'un service sans état. La principale différence est la disponibilité d’un *fournisseur d’état* qui peut stocker l’état de manière fiable. Service Fabric est fourni avec une implémentation de fournisseur d’état appelée [Reliable Collections](service-fabric-reliable-services-reliable-collections.md), qui vous permet de créer des structures de données répliquées via le Gestionnaire d’état fiable. Un Reliable Service avec état utilise ce fournisseur d’état par défaut.

Ouvrez **HelloWorldStateful.cs** dans *HelloWorldStateful* qui contient la méthode RunAsync suivante :

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### RunAsync
`RunAsync()` fonctionne de la même façon dans les services avec ou sans état. Toutefois, dans un service avec état, la plateforme exécute un travail supplémentaire en votre nom avant d’exécuter `RunAsync()`. Ce travail peut inclure de veiller à ce que le Gestionnaire d’état fiable et les Reliable Collections soient prêts à l’emploi.

### Reliable Collections et Gestionnaire d’état fiable
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) est une implémentation de dictionnaire qui peut servir à stocker de façon fiable l’état dans le service. Avec Service Fabric et les collections fiables, vous pouvez stocker des données directement dans votre service sans avoir besoin d’un magasin persistant externe. Les Reliable Collections rendent vos données hautement disponibles. Pour ce faire, Service Fabric crée et gère plusieurs *réplicas* de votre service pour vous. Il fournit également une API qui élimine la complexité de la gestion de ces réplicas et leurs transitions d’état.

Les collections fiables peuvent stocker n’importe quel type .NET, y compris vos types personnalisés, avec quelques inconvénients :

* Service Fabric rend votre état hautement disponible en *répliquant* l’état sur tous les nœuds, et les Reliable Collections stockent vos données sur le disque local de chaque réplica. Cela signifie que tout ce qui est stocké dans les Reliable Collections doit être *sérialisable*. Par défaut, les Reliable Collections utilisent [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) pour la sérialisation. Il est donc important de vérifier que vos types sont [pris en charge par le sérialiseur de contrat de données](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) quand vous utilisez le sérialiseur par défaut.
* Les objets sont répliqués à des fins de haute disponibilité quand vous envoyez des transactions sur des collections fiables. Les objets stockés dans les collections fiables sont conservés dans la mémoire locale de votre service. Cela signifie que vous avez une référence locale à l’objet.
  
   Il est important de ne pas muter les instances locales de ces objets sans effectuer une opération de mise à jour sur la collection fiable dans une transaction. En effet, les modifications des instances locales d’objets ne sont pas répliquées automatiquement. Vous devez réinsérer l’objet dans le dictionnaire ou utiliser l’une des méthodes de *mise à jour* sur le dictionnaire.

Le Gestionnaire d’état fiable gère les Reliable Collections pour vous. Vous pouvez simplement demander au Gestionnaire d’état fiable une collection fiable par son nom à tout moment et à tout emplacement dans votre service. Le Gestionnaire d’état fiable permet de récupérer une référence. Nous ne vous recommandons pas d’enregistrer les références aux instances de la collection fiable dans des variables ou propriétés de membre de classe. Vous devez particulièrement veiller à ce que la référence soit définie sur une instance pendant tout le cycle de vie du service. Ce travail, optimisé pour les visites répétées, est géré pour vous par le Gestionnaire d’état fiable.

### Opérations transactionnelles et asynchrones
```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Les Reliable Collections ont de nombreuses opérations en commun avec leurs équivalents `System.Collections.Generic` et `System.Collections.Concurrent`, sauf LINQ. Les opérations sur les Reliable Collections sont asynchrones. Ceci est dû au fait que les opérations d’écriture avec les Reliable Collections exécutent des opérations E/S pour répliquer et faire persister les données sur le disque.

Les opérations des Reliable Collections sont *transactionnelles*. Ainsi, vous pouvez conserver un état cohérent entre plusieurs Reliable Collections et opérations. Vous pouvez, par exemple, retirer un élément de travail d'une File d'attente fiable, effectuer une opération sur ce dernier et enregistrer le résultat dans un Dictionnaire fiable, le tout dans une transaction unique. Ces opérations sont traitées comme une seule opération atomique, ce qui garantit soit son succès total, soit son échec total. Si une erreur se produit une fois que vous avez retiré l’élément de la file d’attente, mais avant d’enregistrer le résultat, la transaction entière est annulée et l’élément reste dans la file d’attente à des fins de traitement.

## Exécution de l'application
Revenons maintenant à l’application *HelloWorld*. Vous pouvez désormais générer et déployer vos services. Quand vous appuyez sur **F5**, votre application est générée et déployée sur votre cluster local.

Une fois que les services commencent à s’exécuter, vous pouvez afficher les événements ETW (Event Tracing for Windows, Suivi d’événements pour Windows) générés dans une fenêtre **Événements de diagnostic**. Notez que les événements affichés proviennent à la fois du service sans état et du service avec état dans l’application. Vous pouvez interrompre le flux en cliquant sur le bouton **Suspendre**. Vous pouvez ensuite examiner les détails d’un message en développant ce message.

> [!NOTE]
> Avant d’exécuter l’application, assurez-vous qu’un cluster de développement local est en cours d’exécution. Pour plus d’informations sur la configuration de votre environnement local, consultez le [guide de prise en main](service-fabric-get-started.md).
> 
> 

![Afficher les événements de diagnostic dans Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## Étapes suivantes
[Déboguer votre application Service Fabric dans Visual Studio](service-fabric-debugging-your-application.md)

[Prise en main : services de l’API Web Service Fabric avec auto-hébergement OWIN](service-fabric-reliable-services-communication-webapi.md)

[En savoir plus sur les Collections fiables](service-fabric-reliable-services-reliable-collections.md)

[Déployer une application](service-fabric-deploy-remove-applications.md)

[Mise à niveau de l’application](service-fabric-application-upgrade.md)

[Référence du développeur pour les services fiables](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=AcomDC_0713_2016-->