<properties
   pageTitle="Prise en main des services fiables Service Fabric de Microsoft Azure"
   description="Création d’une application Service Fabric avec des services avec et sans état."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="vturecek"/>

# Prise en main des services fiables Service Fabric de Microsoft Azure

Une application Service Fabric contient un ou plusieurs services qui exécutent votre code. Ce didacticiel vous guide tout au long des étapes de création d'applications Service Fabric « Hello World » avec et sans état à l'aide du [modèle de programmation *Services fiables*](../Service-Fabric/service-fabric-reliable-services-introduction.md).

Un service sans état est le type de service qui existe principalement dans des applications cloud actuelles. Le service est considéré comme étant sans état car le service proprement dit ne contient pas de données devant être stockées de manière fiable ou rendues hautement disponibles. En d'autres termes, si une instance d'un service sans état s'arrête, l'intégralité de son état interne est perdue. Dans ces types de services, l'état doit être conservé dans un magasin externe, comme les Tables Azure ou une base de données SQL, pour être hautement disponible et fiable.

Service Fabric présente un nouveau type de service avec état : un service qui peut gérer l'état de manière fiable dans le service lui-même, colocalisé avec le code qui l'utilise. Votre état est rendu hautement disponible par Service Fabric sans avoir besoin de conserver l'état dans un magasin externe.

Dans ce didacticiel, vous allez implémenter un service sans état et un service avec état conservant un compteur interne. Dans le service sans état, la valeur du compteur est perdue lorsque le service redémarre ou se déplace. Toutefois, dans le service avec état, l'état du compteur est rendu fiable par Service Fabric. Ainsi, si l'exécution du service est interrompue pour une raison quelconque au milieu du comptage, elle peut reprendre là où elle s'était arrêtée.

## Créer un service sans état

Commençons par un service sans état.

Lancez Visual Studio 2015 RC comme **Administrateur** et créez un nouveau projet **Service sans état Service Fabric** nommé *HelloWorld* :

![Utiliser la boîte de dialogue Nouveau projet pour créer un service sans état Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

La solution créée contient 2 projets :

 + **HelloWorldApplication** Il s'agit du projet *application* qui contient vos *services*. Il contient également le manifeste d'application qui décrit l'application et plusieurs scripts PowerShell qui vous aideront à déployer votre application.
 + **HelloWorld** : il s'agit du projet service, qui contient la mise en œuvre du service sans état.


## Mettre en œuvre le service

Ouvrez le fichier **HelloWorld.cs** dans le projet de service. Dans Service Fabric, un service peut exécuter n'importe quelle logique métier. L'API de service fournit deux points d'entrée pour votre code :

 - Une méthode de point d'entrée de durée indéterminée appelée *RunAsync* avec laquelle vous pouvez commencer l'exécution de toute charge de travail, comme les charges de travail de calcul de longue durée.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Un point d'entrée de communication où vous pouvez connecter la pile de communication de votre choix, comme l'API Web, vous permettant de recevoir des requêtes d'utilisateurs ou d'autres services.

```C#
protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}
```

Dans ce didacticiel, nous allons nous concentrer sur la méthode de point d'entrée `RunAsync()` où vous pouvez commencer à exécuter du code immédiatement. Le modèle de projet inclut un exemple de mise en œuvre de `RunAsync()` qui incrémente un comptage continu.

> [AZURE.NOTE]Pour plus d'informations sur l'utilisation d'une pile de communication, consultez [Prise en main des services d'API Web de Microsoft Azure Service Fabric avec l'auto-hébergement OWIN](service-fabric-reliable-services-communication-webapi.md)


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.

    int iterations = 0;
    while (!cancellationToken.IsCancellationRequested)
    {
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);
        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

La plateforme appelle cette méthode lorsqu'une instance de votre service est placée et prête à être exécutée. Pour les services sans état, cela signifie simplement lorsque l'instance de service est ouverte. Un jeton d'annulation est fourni pour coordonner lorsque l'instance de service doit être fermée. Dans Service Fabric, ce cycle d'ouverture / fermeture d'une instance de service peut se produire plusieurs fois au cours de la durée de vie de votre service dans son ensemble pour diverses raisons, notamment :

- Le système peut déplacer les instances de service pour l'équilibrage des ressources.
- En cas de défaillance.
- Pendant les mises à niveau du système ou de l’application.
- Lorsque le matériel sous-jacent connaît une panne. 

Cette orchestration est gérée par le système afin de conserver votre service hautement disponible et correctement équilibré.

`RunAsync()` est exécuté dans sa propre **Tâche**. Notez que dans l'extrait de code ci-dessus nous entrons directement dans une boucle **while** : il n'est pas nécessaire de planifier une tâche distincte pour votre charge de travail. L'annulation de votre charge de travail est un effort conjoint orchestré par le jeton d'annulation fourni. Le système attendra la fin de la tâche (suite à la réussite, l’annulation ou la défaillance) avant de poursuivre. Il est **important** de respecter le jeton d'annulation, de terminer le travail et de quitter `RunAsync()` aussi rapidement que possible lorsqu’une annulation est demandée par le système.

Dans cet exemple de service sans état, le décompte est stocké dans une variable locale. Mais comme il s'agit d'un service sans état, la valeur stockée existe uniquement pour le cycle de vie actuel de l'instance de service dans laquelle elle se trouve. Lorsque le service se déplace ou redémarre, la valeur est perdue.

## Créer un service avec état

Pour convertir notre valeur de compteur de sans état à hautement disponible et persistante même lorsque le service se déplace ou redémarre, nous avons besoin d'un service avec état.

Dans la même application **HelloWorld**, ajoutez un nouveau service en cliquant avec le bouton droit de la souris sur le projet d'application et en sélectionnant **Nouveau Service Fabric**.

![Ajouter un service à votre application Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Sélectionnez **Service avec état Service Fabric** et nommez-le « HelloWorldStateful ». Cliquez sur **Ajouter**.

![Utiliser la boîte de dialogue Nouveau projet pour créer un service avec état Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Votre application doit maintenant contenir deux services : le service sans état *HelloWorld* et le service avec état *HelloWorldStateful*.

Ouvrez **HelloWorldStateful.cs** dans *HelloWorldStateful* qui contient la méthode `RunAsync` suivante :

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (!cancellationToken.IsCancellationRequested)
    {
        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            ServiceEventSource.Current.ServiceMessage(
                this,
                "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

### RunAsync

Un service avec état a les mêmes points d'entrée qu'un service sans état. La principale différence est la disponibilité de *Collections fiables* et du *Gestionnaire d'état*. Dans un service avec état, `RunAsync()` fonctionne comme un service sans état ; sauf que dans un service avec état, la plateforme exécute du travail supplémentaire de votre part avant d'exécuter `RunAsync()`, comme, par exemple, vérifier que le *Gestionnaire d'état* et les *Collections fiables* sont prêts à être utilisés.

### Collections fiables et Gestionnaire d'état

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

**IReliableDictionary** est une implémentation de dictionnaire qui vous permet de stocker de façon fiable l'état du service. Il fait partie des [Collections fiables](service-fabric-reliable-services-reliable-collections.md) intégrées de Service Fabric. Avec Service Fabric et les Collections fiables, vous pouvez désormais stocker directement des données dans votre service sans avoir besoin d'un magasin persistant externe, ce qui donne une haute disponibilité à vos données. Service Fabric achève cela en créant et en gérant plusieurs *réplicas* de votre service pour vous, tout en fournissant une API qui élimine les complexités liées à la gestion de ces réplicas et leurs transitions d'état.

Les Collections fiables peuvent stocker n'importe quel type .NET (y compris vos types personnalisés) avec quelques inconvénients :

 1. Service Fabric rend votre état hautement disponible en *répliquant* l'état entre les nœuds et en le stockant sur le disque local. Cela signifie que tout ce qui est stocké dans une Collection fiable doit être *sérialisable*. Par défaut, les Collections fiables utilisent [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) pour la sérialisation. Donc, il est important de s'assurer que vos types sont [pris en charge par le sérialiseur de contrat de données](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) lorsque vous utilisez le sérialiseur par défaut.

 2. Les objets sont répliqués pour une haute disponibilité lorsque vous engagez une transaction sur une Collection fiable. Les objets stockés dans les Collections fiables sont conservés dans la mémoire locale de votre service, ce qui signifie que vous avez une référence locale à l'objet.
 
    Il est important de ne pas muter les instances locales de ces objets sans effectuer une opération de mise à jour de la Collection fiable dans une transaction, car ces modifications ne seront pas répliquées automatiquement.

Le *StateManager* se charge de la gestion des Collections fiables pour vous. Il vous suffit de demander au StateManager une collection fiable par son nom, n'importe quand et n'importe où dans votre service et il garantit que vous obteniez une référence en retour. Il n'est pas recommandé d'enregistrer les références aux instances de la Collection fiable dans les propriétés ou les variables de membre de la classe. En effet, il est nécessaire de porter une attention particulière pour garantir que la référence est définie à une instance à tout moment du cycle de vie du service. Ce travail, optimisé pour les visites répétées, est géré pour vous par StateManager.

### Opérations transactionnelle et asynchrones

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Les Collections fiables ont de nombreuses opérations en commun avec leurs équivalents `System.Collections.Generic` et `System.Collections.Concurrent`, notamment LINQ. Toutefois, les opérations sur les Collections fiables sont asynchrones. En effet, les opérations d'écriture sur les Collections fiables sont *répliquées*. Autrement dit, l'opération est envoyée aux autres réplicas du service sur des nœuds différents pour garantir la haute disponibilité.

Les Collections fiables prennent également en charge les opérations *transactionnelles* pour vous permettre de conserver un état cohérent entre plusieurs Collections fiables. Vous pouvez, par exemple, retirer un élément de travail d'une File d'attente fiable, effectuer une opération sur ce dernier et enregistrer le résultat dans un Dictionnaire fiable, le tout dans une transaction unique. Ceci est traité comme une opération atomique, ce qui permet de garantir que soit toute l'opération réussira, soit aucune partie de l'opération ne réussira. Donc, si une erreur se produit après que vous avez retiré l'élément de la file d'attente mais avant l'enregistrement du résultat, la transaction entière est annulée et l'élément reste dans la file d'attente pour traitement.

## Exécution de l'application

Retour à l’application *HelloWorld*. Vous pouvez désormais générer et déployer vos services. Appuyez sur **F5** et votre application sera générée et déployée dans le cluster local.

Une fois que les services sont en cours d'exécution, vous pouvez voir les événements ETW générés dans une fenêtre **Événements de diagnostic**. Notez qu'il existe des événements affichés à partir du service sans état et du service avec état dans l'application. Vous pouvez interrompre le flux en cliquant sur le bouton *Suspendre* et examiner les détails du message en développant un message.

>[AZURE.NOTE]Avant d'exécuter l'application, assurez-vous qu'un cluster de développement local est en cours d'exécution. Consultez le [Guide de prise en main](service-fabric-get-started.md) pour configurer votre environnement local.

![Afficher les événements de diagnostic dans Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## Étapes suivantes

[Débogage de votre application Service Fabric dans Visual Studio](service-fabric-debugging-your-application.md)

[Prise en main des services d'API Web de Microsoft Azure Service Fabric avec l'auto-hébergement OWIN](service-fabric-reliable-services-communication-webapi.md)

[En savoir plus sur les Collections fiables](service-fabric-reliable-services-reliable-collections.md)

[Gérer un service Service Fabric](service-fabric-manage-your-service-index.md)

[Référence du développeur pour les services fiables](https://msdn.microsoft.com/library/azure/dn706529.aspx)
 

<!---HONumber=July15_HO2-->