<properties
   pageTitle="Polymorphisme dans l’infrastructure Actor | Microsoft Azure"
   description="Concevez des hiérarchies d’interfaces et de types .NET dans l’infrastructure Reliable Actors afin de réutiliser des fonctionnalités et des définitions d'API."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/01/2015"
   ms.author="seanmck"/>

# Polymorphisme dans l’infrastructure Reliable Actor

L'infrastructure Reliable Actor simplifie la programmation des systèmes distribués en vous permettant de concevoir votre service à l'aide des nombreuses techniques identiques à celles que vous utiliseriez dans la conception orientée objet. L’une de ces techniques est le polymorphisme, qui permet à des types et à des interfaces d’hériter de parents plus généralisés. L'héritage dans l’infrastructure d’acteur suit généralement le modèle .NET avec quelques contraintes supplémentaires.

## Interfaces

L'infrastructure d'acteur exige que vous définissiez au moins une interface à implémenter par votre type d'acteur. Cette interface est utilisée pour générer une classe proxy, utilisable par des clients pour communiquer avec vos acteurs. Les interfaces peuvent hériter d'autres interfaces tant que toutes les interfaces implémentées par un type et tous ses parents dérivent en définitive d’IActor, l'interface de base définie par la plateforme pour les acteurs. Par conséquent, voici à quoi peut ressembler un exemple de polymorphisme classique qui utilise des formes :

![Hiérarchie d'interfaces pour les acteurs de forme][shapes-interface-hierarchy]


## Types

Vous pouvez également créer une hiérarchie de types d'acteur, dérivée de la classe de base Actor fournie par la plateforme. Pour les acteurs avec état, vous pouvez même créer une hiérarchie de types d'état. Dans le cas de formes, vous pouvez avoir un type `Shape` de base avec un type d'état de `ShapeState`.

    public abstract class Shape : Actor<ShapeState>, IShape
    {
        ...
    }

Les sous-types de `Shape` peuvent utiliser des sous-types de `ShapeType` pour le stockage de propriétés plus spécifiques.

    [ActorService(Name = "Circle")]
    public class Circle : Shape, ICircle
    {
        private CircleState CircleState => this.State as CircleState;

        public override ShapeState InitializeState()
        {
            return new CircleState();
        }

        [Readonly]
        public override Task<int> GetVerticeCount()
        {
            return Task.FromResult(0);
        }

       [Readonly]
       public override Task<double> GetArea()
       {
           return Task.FromResult(
               Math.PI*
               this.CircleState.Radius*
               this.CircleState.Radius);
       }

       ...
    }

Notez l’attribut `ActorService` sur le type d'intervenant. Cela indique au kit de développement logiciel (SDK) de Service Fabric qu’il doit automatiquement créer un service pour l'hébergement d’acteurs de ce type. Dans certains cas, vous pouvez souhaiter créer un type de base destiné exclusivement à la fonctionnalité de partage avec des sous-types, qui ne sera jamais utilisé pour instancier des acteurs concrets. Dans ce cas, vous devez utiliser le mot clé `abstract` pour indiquer que vous ne créerez jamais d’acteur basé sur ce type.


## Étapes suivantes

- Découvrez [comment l'infrastructure Reliable Actors s'appuie sur la plateforme Service Fabric](service-fabric-reliable-actors-platform.md) pour fournir une fiabilité, une évolutivité et une cohérence
- En savoir plus sur le [cycle de vie des acteurs](service-fabric-reliable-actors)

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

<!---HONumber=AcomDC_1203_2015-->