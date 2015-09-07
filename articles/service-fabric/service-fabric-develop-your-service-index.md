<properties
   pageTitle="Développer un service Service Fabric"
	description="Informations conceptuelles et didacticiels qui vous permettent de comprendre comment développer un service Service Fabric à l'aide des modèles de programmation d'acteur fiable et de services fiables."
	services="service-fabric"
	documentationCenter=".net"
	authors="rwike77"
	manager="timlt"
	editor=""/>

<tags
   ms.service="service-fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="NA"
	ms.date="08/20/2015"
	ms.author="ryanwi"/>

# Développer un service Service Fabric
Cette page contient des liens vers des articles de vue d'ensemble et conceptuels ainsi qu'un didacticiel pour vous aider à développer un service Service Fabric. Service Fabric propose deux modèles de programmation de haut niveau pour la création de services : les API d'acteurs fiables et les API de services fiables. Si les deux modèles reposent sur le même noyau Service Fabric, ils offrent différents compromis entre simplicité et flexibilité en termes de concurrence, partitionnement et communication. Vous devez comprendre les deux modèles pour choisir l'infrastructure appropriée pour un service particulier dans votre application.

- [Choisir un modèle de programmation](service-fabric-choose-framework.md)
- [Présentation du modèle d'acteur Service Fabric](service-fabric-reliable-actors-introduction.md)
- [Présentation du modèle de programmation de services fiables](../Service-Fabric/service-fabric-reliable-services-introduction.md)

## Modèle de programmation d'acteurs fiables
 Les acteurs fiables fournissent un modèle d'acteur asynchrone, monothread. Les acteurs représentent les unités d'état et de calcul distribuées dans le cluster pour atteindre un haut niveau d'extensibilité. Le modèle d'acteur fiable s'appuie sur le magasin distribué fourni par la plateforme Service Fabric sous-jacente pour permettre une gestion d'état hautement disponible et cohérente pour les développeurs d'applications. Pour plus d'informations, consultez les articles suivants :

- [Prise en main des acteurs fiables](service-fabric-reliable-actors-get-started.md)
- [Cycle de vie des acteurs et Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
- [Comment Fabric Actors utilise la plateforme Service Fabric](service-fabric-reliable-actors-platform.md)
- [Remarques sur la sérialisation de type dans Azure Service Fabric Actors](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

La communication avec les acteurs est décrite dans les articles suivants :

- [Présentation du modèle d'acteur Service Fabric](service-fabric-reliable-actors-introduction.md#actor-communication).
- [Communication avec les services](service-fabric-connect-and-communicate-with-services.md)

Ces articles décrivent des scénarios et des modèles de conception utiles :

- [Modèles de conception du modèle d'acteur](service-fabric-reliable-actors-patterns-introduction.md)  
- [Modèle : cache intelligent](service-fabric-reliable-actors-pattern-smart-cache.md)
- [Modèle : Réseaux distribués et graphiques](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)
- [Modèle : Gestion des ressources](service-fabric-reliable-actors-pattern-resource-governance.md)
- [Modèle : composition d'un service avec état](service-fabric-reliable-actors-pattern-stateful-service-composition.md)
- [Modèle : Internet des objets](service-fabric-reliable-actors-pattern-internet-of-things.md)
- [Modèle : Calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md)
- [Quelques anti-modèles](service-fabric-reliable-actors-anti-patterns.md)

Un simple accès concurrentiel en alternance est fourni pour les méthodes d'acteur fiable. Accès concurrentiel, minuteries et rappels, et réentrance sont décrits dans les articles suivants :

- [Accès concurrentiel](service-fabric-reliable-actors-introduction.md#concurrency)
- [Événements et compteurs de performance liés à l'accès concurrentiel](service-fabric-reliable-actors-diagnostics.md)
- [Réentrance des acteurs](service-fabric-reliable-actors-reentrancy.md)
- [Minuteries des acteurs](service-fabric-reliable-actors-timers-reminders.md)

Pour plus d'informations sur la configuration des acteurs fiables, consultez les articles suivants :

- [Configuration de KVSActorStateProvider](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)  
- [Configuration des acteurs fiables - ReliableDictionaryActorStateProvider](../service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

Les acteurs fiables émettent des événements et des compteurs de performance, qui peuvent être utilisés pour diagnostiquer et surveiller votre service :

- [Diagnostics des acteurs](service-fabric-reliable-actors-diagnostics.md)
- [Événements des acteurs](service-fabric-reliable-actors-events.md)


## Modèle de programmation de services fiables
Les services fiables vous offrent un modèle de programmation de niveau supérieur, simple et puissant pour vous aider à exprimer ce qui est important pour votre application. Pour plus d'informations, consultez les articles suivants :

- [Prise en main des services fiables](service-fabric-reliable-services-quick-start.md)
- [Vue d'ensemble du modèle de programmation](../service-fabric-reliable-services-service-overview.md)  
- [Architecture](service-fabric-reliable-services-platform-architecture.md)
- [Collections fiables](service-fabric-reliable-services-reliable-collections.md)
- [Configuration des services fiables avec état](../Service-Fabric/service-fabric-reliable-services-configuration.md)
- [Utilisation avancée du modèle de programmation de services fiables](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md)

La communication avec des services fiables et les abstractions que les clients peuvent utiliser pour détecter les points de terminaison de service et communiquer avec eux sont décrites dans les articles suivants :

- [Communication avec les services](service-fabric-connect-and-communicate-with-services.md)
- [Modèle de communication de service](service-fabric-reliable-services-communication.md)
- [Pile de communication par défaut fournie par l'infrastructure des services fiables](service-fabric-reliable-services-communication-default.md)
- [Pile de communication WCF pour les services fiables](service-fabric-reliable-services-communication-wcf.md)
- [Prise en main des services d'API Web de Microsoft Azure Service Fabric avec l'auto-hébergement OWIN (VS 2015 RC)](service-fabric-reliable-services-communication-webapi.md)

Les services fiables émettent des événements et des compteurs de performance, qui peuvent être utilisés pour diagnostiquer et surveiller votre service :

- [Diagnostics de service fiable avec état](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=August15_HO9-->