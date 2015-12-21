<properties
   pageTitle="Modèles et anti-modèles Reliable Actors | Microsoft Azure"
   description="Présente le modèle de programmation d'acteur, des modèles de conception qui fonctionnent correctement avec Service Fabric Reliable Actors et certains anti-modèles à éviter."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# Présentation des modèles de conception pour les Acteurs fiables

Le modèle de programmation Acteurs fiables de Service Fabric est une plateforme basée sur le modèle d'acteur permettant de résoudre des problèmes concrets à l'échelle du cloud. Azure Service Fabric est une plateforme permettant de créer, à la fois pour le cloud et localement, des applications évolutives, extrêmement fiables et faciles à développer et à gérer. Cet article sert de document pratique pour résoudre des problèmes pratiques. Après avoir parcouru les différents modèles, vous devriez être en mesure de comprendre comment utiliser le modèle Reliable Actor pour créer des solutions « entreprise » ou « cloud ».

## Modèles

Dans cette section, nous dresserons une liste des modèles et des scénarios associés que nous avons étudiés au cours de nos discussions avec les clients. Ces modèles représentent des classes de problèmes applicables à un large éventail de solutions que nos clients créent grâce à Microsoft Azure. Bien que ces scénarios reposent sur des cas réels, nous avons supprimé la plupart des problèmes spécifiques à un domaine pour rendre les modèles plus clairs pour le lecteur. Vous constaterez que la plupart des exemples de codes sont simples ou évidents. Nous incluons ce code par souci d'exhaustivité et non parce qu'il est particulièrement remarquable.

Les modèles présentés dans ce document ne sont pas destinés à être complets ou canoniques : certains développeurs peuvent résoudre le même problème ou proposer un modèle d'une autre manière.

[Modèle : cache intelligent](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modèle : Réseaux distribués et graphiques](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modèle : Gestion des ressources](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modèle : composition d'un service avec état](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modèle : Internet des objets](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modèle : Calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Quelques anti-modèles](service-fabric-reliable-actors-anti-patterns.md)

### En savoir plus sur les acteurs, un bref historique

Le [livre](http://dl.acm.org/citation.cfm?id=1624804) écrit par Hewitt et al. qui est à l'origine du modèle d'acteur a été publié en 1973, mais ce n'est que relativement récemment que le modèle d'acteur a connu une certaine popularité comme solution pour gérer les problèmes d'accès simultané et la complexité des systèmes distribués. Le modèle d'acteur prend en charge des objets individuels granulaires — acteurs — isolés les uns des autres. Ils communiquent via des messages asynchrones, ce qui permet des communications directes entre les acteurs. Un acteur s'exécute avec une sémantique à thread unique. Combiné à l'encapsulation de l'état de l'acteur et à son isolement des autres acteurs, cela simplifie l'écriture de systèmes hautement parallèles en supprimant les problèmes d'accès simultané au niveau du code de l'acteur. Les acteurs sont créés dynamiquement sur le pool de ressources matérielles disponibles.

[Erlang](http://www.erlang.org/) est l'implémentation de modèle d'acteur la plus populaire. Les développeurs ont commencé à redécouvrir le modèle d'acteur, ce qui a entraîné un intérêt grandissant pour le modèle Erlang et la création de nouvelles solutions de type Erlang : les acteurs [Scala](http://www.scala-lang.org/), [Akka](http://akka.io), [Akka.net](http://getakka.net/), [DCell](http://research.microsoft.com/pubs/75988/dcell.pdf).

## Bref aperçu d'Azure Service Fabric

Reliable Actors est une implémentation du modèle d'acteur qui reprend certaines idées du modèle Erlang et des systèmes d'objets distribués. Elle ajoute une couche d'indirection au niveau des acteurs et les présente dans un modèle de programmation intégré qui s'appuie sur la plateforme Azure Service Fabric.

Les principaux avantages de Reliable Actors sont : 1) **productivité des développeurs**, même pour les programmeurs débutants ; et 2) **évolutivité transparente par défaut** sans aucun effort particulier de la part du programmeur. Reliable Actors est une bibliothèque .NET qui s'exécute par dessus Service Fabric et des outils qui facilitent le développement d'applications distribuées complexes et rendent les applications résultantes évolutives par conception. Nous allons développer chacun de ces avantages ci-dessous. Le modèle de programmation améliore la productivité des programmeurs expérimentés et débutants en fournissant les abstractions clés, les garanties et les services système suivants.

* *Paradigme de programmation orientée objet (OOP) familier*. Les acteurs sont des classes .NET qui implémentent des interfaces d'acteur .NET déclarées avec des méthodes et des propriétés asynchrones. Ainsi, les acteurs apparaissent au programmeur en tant qu'objets distants dont les méthodes/propriétés peuvent être appelées directement. Cela fournit au programmeur le paradigme OOP familier qui transforme les appels de méthode en messages, les achemine vers les points de terminaison correspondants, appelant les méthodes de l'acteur cible et gérant les défaillances et les cas extrêmes d'une manière totalement transparente.

* *Exécution avec thread unique des acteurs.* Le runtime Reliable Actors garantit qu'un acteur ne s'exécute jamais sur plusieurs threads simultanément. Combiné à l'isolement des autres acteurs, le programmeur n'est jamais confronté à un problème d'accès simultané au niveau de l'acteur et, par conséquent, n'a jamais besoin d'utiliser des verrous ou autres mécanismes de synchronisation pour contrôler l'accès aux données partagées. Cette caractéristique seule facilite le développement des applications distribuées pour les programmeurs débutants.

* *Activation transparente.* Le runtime active un acteur en fonction des besoins, uniquement lorsqu'il y a un message à traiter. Cela distingue clairement la notion de création logique d'un acteur, visible et contrôlée par le code d'application, et l'activation physique de l'acteur en mémoire, transparente pour l'application. Reliable Actors s'apparente à la mémoire virtuelle dans la mesure où il décide quand effectuer un « page out » (désactiver) ou un « page » (activer) un acteur ; l'application dispose d'un accès ininterrompu à l'ensemble de « l'espace mémoire » des acteurs logiquement créés, qu'ils figurent ou non dans la mémoire physique à un point particulier dans le temps. L'activation transparente permet un équilibrage de la charge dynamique et adaptatif via le placement et la migration des acteurs dans l'ensemble du pool de ressources matérielles.

* *Transparence des emplacements.* Une référence d'acteur (objet proxy) que le programmeur utilise pour appeler les méthodes de l'acteur ou pour passer à d'autres composants contient uniquement l'identité logique de l'acteur. La traduction de l'identité logique de l'acteur vers son emplacement physique et le routage correspondant des messages sont effectués de manière transparente par le runtime Reliable Actors. Le code d'application communique avec les acteurs, quel que soit leur emplacement physique, qui peut changer au fil du temps en raison de pannes ou de la gestion des ressources, ou parce qu'un acteur est désactivé au moment où il est appelé.

* *Intégration transparente avec le magasin persistant.* Reliable Actors permet un mappage déclaratif de l'état en mémoire des acteurs vers le magasin persistant. Il synchronise les mises à jour de façon transparente, ce qui garantit que les appelants reçoivent les résultats uniquement après la mise à jour de l'état persistant.

* *Haute disponibilité, prise en charge du basculement et gestion du cycle de vie des applications.* L'état d'Actors est géré par la plateforme et répliqué de façon à ce qu'il puisse être restauré si, par exemple, un nœud du cluster est défaillant. Service Fabric gère également le cycle de vie des applications et permet de les mettre à niveau sans temps d'arrêt. Le modèle de programmation Reliable Actors est conçu pour accompagner avec succès les programmeurs dans la mise à l'échelle de leur application ou service selon plusieurs ordres de grandeur. Ce processus s'effectue en incorporant les meilleurs modèles et pratiques éprouvés et en fournissant une implémentation efficace de fonctionnalités système de niveau inférieur. Voici quelques facteurs clés qui favorisent l'évolutivité et les performances des applications Service Fabric.

* *Le partitionnement implicite granulaire de l'état de l'application.* Un utilisant des acteurs comme des entités directement adressables, les programmeurs décomposent implicitement l'état général de leurs applications. Bien que le modèle de programmation Reliable Actors n'impose pas la taille de l'acteur, il est judicieux dans la plupart des cas de disposer d'un grand nombre d'acteurs (plusieurs millions ou plus), chacun représentant une entité naturelle de l'application, par exemple un compte d'utilisateur, un bon de commande, etc. Avec des acteurs adressables individuellement et leur emplacement physique géré par le runtime, il existe une grande flexibilité dans l'équilibrage de la charge et le traitement des zones sensibles de manière transparente et générique, sans aucune intervention du développeur de l'application.

* *Gestion adaptative des ressources.* Comme les acteurs n'émettent aucune hypothèse sur l'emplacement des autres acteurs avec lesquels ils interagissent et en raison de la transparence de ces emplacements, le runtime peut gérer et ajuster l'allocation des ressources matérielles disponibles de façon très dynamique en prenant des décisions très précises sur le placement ou la migration des acteurs dans l'ensemble du cluster de calcul en réaction à des modèles de charge et de communication, sans faire échouer les demandes entrantes. En créant plusieurs réplicas d'un acteur particulier, le runtime peut augmenter le débit de l'acteur si nécessaire sans apporter de modifications au code de l'application.

* *Communication multiplexée.* Les acteurs dans Service Fabric disposent de points de terminaison logiques, et la messagerie entre eux est multiplexée sur un ensemble fixe de connexions physiques tous-vers-tous (sockets TCP). Cela permet au runtime d'héberger un très grand nombre (plusieurs millions) d'entités adressables sans aucune surcharge zéro par acteur une aucun frais de système d'exploitation par acteur. En outre, l'activation/désactivation d'un acteur n'entraîne pas de coût d'inscription/désinscription d'un système d'extrémité physique, tel qu'un port TCP ou une URL HTTP.

* *Planification efficace.* Le runtime Reliable Actors planifie l'exécution des planifications d'un grand nombre d'acteurs à thread unique dans l'ensemble d'un pool de threads personnalisé avec un thread par cœur de processeur physique. Comme le code de l'acteur est écrit dans un style de continuation sans blocage (une exigence du modèle de programmation), le code de l'application s'exécute avec un thread unique d'une façon « coopérative » très efficace et sans aucun conflit. Cela permet au système d'atteindre un débit élevé et de s'exécuter avec une très forte sollicitation du processeur (jusqu'à 90 + %) et une grande stabilité. Le fait que la croissance du nombre d'acteurs dans le système et que la charge n'entraînent pas de threads supplémentaires ou d'autres primitives de système d'exploitation favorise l'évolutivité des nœuds individuels et de l'ensemble du système.

* *Comportement asynchrone explicite.* Le modèle de programmation Reliable Actors rend explicite la nature asynchrone d'une application distribuée et incite les programmeurs à écrire un code asynchrone non bloquant. Cela garantit un grand degré de parallélisme distribué et un débit global sans utilisation explicite de plusieurs threads.

<!---HONumber=AcomDC_1210_2015-->