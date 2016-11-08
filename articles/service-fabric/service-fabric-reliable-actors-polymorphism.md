---
title: Polymorphisme dans l’infrastructure Reliable Actors | Microsoft Docs
description: Concevez des hiérarchies d’interfaces et de types .NET dans l’infrastructure Reliable Actors afin de réutiliser des fonctionnalités et des définitions d'API.
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/07/2016
ms.author: seanmck

---
# Polymorphisme dans l’infrastructure Reliable Actors
L’infrastructure Reliable Actors vous permet de générer des acteurs en utilisant bon nombre des mêmes techniques que celles employées dans la conception orientée objet. L’une de ces techniques est le polymorphisme, qui permet à des types et à des interfaces d’hériter de parents plus généralisés. L’héritage dans l’infrastructure Reliable Actors suit généralement le modèle .NET avec quelques contraintes supplémentaires.

## Interfaces
L’infrastructure Reliable Actors exige que vous définissiez au moins une interface que votre type d’acteur doit implémenter. Cette interface est utilisée pour générer une classe proxy, utilisable par des clients pour communiquer avec vos acteurs. Les interfaces peuvent hériter d’autres interfaces tant que toutes les interfaces implémentées par un type d’acteur et tous ses parents dérivent finalement d’IActor. IActor est l’interface de base définie par la plateforme pour les acteurs. Par conséquent, voici à quoi peut ressembler un exemple de polymorphisme classique qui utilise des formes :

![Hiérarchie d'interfaces pour les acteurs de forme][shapes-interface-hierarchy]

## Types
Vous pouvez également créer une hiérarchie des types d’acteur, qui sont dérivés de la classe de base Actor fournie par la plateforme. Dans le cas de formes, vous pouvez avoir un type `Shape` de base :

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```

Les sous-types de `Shape` peuvent remplacer des méthodes à partir de la base.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```

Notez l’attribut `ActorService` sur le type d'intervenant. Cet attribut indique à l’infrastructure Reliable Actor qu’elle doit automatiquement créer un service pour héberger des acteurs de ce type. Dans certains cas, vous pouvez souhaiter créer un type de base destiné exclusivement à la fonctionnalité de partage avec des sous-types, qui ne sera jamais utilisé pour instancier des acteurs concrets. Dans ce cas, vous devez utiliser le mot clé `abstract` pour indiquer que vous ne créerez jamais d’acteur basé sur ce type.

## Étapes suivantes
* Découvrez [comment l’infrastructure Reliable Actors s’appuie sur la plateforme Service Fabric](service-fabric-reliable-actors-platform.md) pour garantir la fiabilité, l’extensibilité et la cohérence.
* En savoir plus sur le [cycle de vie des acteurs](service-fabric-reliable-actors-lifecycle.md)

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

<!---HONumber=AcomDC_0713_2016-->