<properties
   pageTitle="Modèles et anti-modèles Reliable Actors | Microsoft Azure"
   description="Présente le modèle de programmation d’acteur, des modèles de conception qui fonctionnent correctement avec Service Fabric Reliable Actors et certains anti-modèles à éviter."
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

Le modèle de programmation Reliable Actors dans Azure Service Fabric est une plateforme basée sur le modèle d’acteur permettant de résoudre des problèmes concrets à l’échelle du cloud. Service Fabric est une plateforme permettant de créer des applications évolutives, extrêmement fiables et faciles à développer et à gérer, à la fois pour le cloud et localement.

Cet article sert de présentation pratique pour résoudre des problèmes pratiques. Après avoir parcouru les différents modèles, vous devriez comprendre comment utiliser le modèle Reliable Actors pour créer des solutions d’entreprise et cloud.

## Modèles

Dans cette section, nous dresserons une liste des modèles et des scénarios associés que nous avons établis au cours de nos discussions avec les clients. Ces modèles représentent des classes de problèmes applicables à un large éventail de solutions que nos clients créent grâce à Microsoft Azure. Bien que ces scénarios reposent sur des cas réels, nous avons supprimé la plupart des problèmes spécifiques à un domaine pour rendre les modèles plus clairs. Vous constaterez que la plupart des exemples de codes sont simples ou évidents. Nous avons inclus le code par souci d’exhaustivité et non parce qu’il est particulièrement remarquable.

Les modèles présentés ici ne sont pas destinés à être complets ou canoniques. Certains développeurs peuvent résoudre les mêmes problèmes ou modèles d’une autre manière.

[Modèle : cache intelligent](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modèle : réseaux distribués et graphiques](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modèle : gestion des ressources](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modèle : composition d’un service avec état](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modèle : Internet des objets](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modèle : calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Quelques anti-modèles](service-fabric-reliable-actors-anti-patterns.md)

### Acteurs, un bref historique

Le [livre](http://dl.acm.org/citation.cfm?id=1624804) coécrit par Carl Hewitt qui est à l’origine du modèle d’acteur a été publié en 1973. Toutefois, ce n’est que relativement récemment que le modèle d’acteur a connu une certaine popularité comme solution pour gérer les problèmes d’accès simultané et la complexité des systèmes distribués.

Le modèle d’acteur prend en charge des objets individuels granulaires — acteurs — isolés les uns des autres. Ils communiquent par le biais de messages asynchrones, ce qui permet des communications directes entre les acteurs. Un acteur s’exécute en utilisant une sémantique à thread unique. Combiné à l’encapsulation de l’état de l’acteur et à son isolement des autres acteurs, cela simplifie l’écriture de systèmes hautement parallèles, grâce à la suppression des problèmes d’accès simultané au niveau du code de l’acteur. Les acteurs sont créés dynamiquement sur le pool de ressources matérielles disponibles.

[Erlang](http://www.erlang.org/) est l'implémentation de modèle d'acteur la plus populaire. Les développeurs ont commencé à redécouvrir le modèle d’acteur, ce qui a entraîné un intérêt grandissant pour le modèle Erlang et la création de nouvelles solutions de type Erlang, telles que les acteurs [Scala](http://www.scala-lang.org/), [Akka](http://akka.io), [Akka.NET](http://getakka.net/) et [DCell](http://research.microsoft.com/pubs/75988/dcell.pdf).

## Avantages de Service Fabric Reliable Actors

Le modèle de programmation Reliable Actors est une implémentation du modèle d’acteur qui reprend certaines idées du modèle Erlang et des systèmes d’objets distribués. Il ajoute une couche d’indirection au niveau des acteurs et présente ceux-ci dans un modèle de programmation intégré qui s’appuie sur la plateforme Service Fabric.

Les principaux avantages de Reliable Actors sont la **productivité des développeurs**, même pour les programmeurs débutants, et l’**évolutivité transparente par défaut** sans aucun effort particulier de la part des programmeurs. Le modèle de programmation Reliable Actors utilise une bibliothèque .NET qui s’exécute sur Service Fabric. Il fournit des outils qui facilitent sensiblement le développement d’applications distribuées complexes. En outre, ces outils rendent les applications résultantes naturellement évolutives. Nous allons développer chacun de ces avantages ci-dessous. Le modèle de programmation améliore la productivité des programmeurs expérimentés et débutants en fournissant les abstractions clés, les garanties et les services système suivants :

* *Paradigme de programmation orientée objet (OOP) familier*. Les acteurs sont des classes .NET qui implémentent des interfaces d'acteur .NET déclarées avec des méthodes et des propriétés asynchrones. Ainsi, les acteurs apparaissent aux programmeurs en tant qu’objets distants dont les méthodes et les propriétés peuvent être appelées directement. Cela fournit aux programmeurs le paradigme OOP familier qui transforme les appels de méthode en messages, les achemine vers les points de terminaison correspondants, appelant les méthodes de l’acteur cible et gérant les défaillances et les cas extrêmes d’une manière totalement transparente.

* *Exécution avec thread unique des acteurs*. Le runtime Reliable Actors garantit qu'un acteur ne s'exécute jamais sur plusieurs threads simultanément. Comme, en outre, chaque acteur est isolé des autres acteurs, les programmeurs ne sont jamais confrontés à un problème d’accès simultané au niveau de l’acteur. Ils n’ont jamais besoin d’utiliser des verrous ou autres mécanismes de synchronisation pour contrôler l’accès aux données partagées. Cette caractéristique seule facilite le développement des applications distribuées pour les programmeurs débutants.

* *Activation transparente*. Le runtime active un acteur en fonction des besoins, uniquement quand il y a un message à traiter. Cela distingue clairement la notion de création logique d’un acteur, visible et contrôlée par le code d’application, de l’activation physique de l’acteur en mémoire, transparente pour l’application. L’approche Reliable Actors est similaire à la mémoire virtuelle dans la mesure où elle décide quand désactiver (« page out ») ou activer (« page in ») un acteur. L’application dispose d’un accès ininterrompu à l’ensemble de « l’espace mémoire » des acteurs logiquement créés, qu’ils figurent ou non dans la mémoire physique à un point particulier dans le temps. L'activation transparente permet un équilibrage de la charge dynamique et adaptatif via le placement et la migration des acteurs dans l'ensemble du pool de ressources matérielles.

* *Transparence des emplacements*. Une référence d’acteur (objet proxy) qu’un programmeur utilise pour appeler les méthodes d’un acteur ou pour les passer à d’autres composants contient uniquement l’identité logique de l’acteur. La traduction de l'identité logique de l'acteur vers son emplacement physique et le routage correspondant des messages sont effectués de manière transparente par le runtime Reliable Actors. Le code d’application communique avec les acteurs, quel que soit leur emplacement physique, qui peut changer au fil du temps en raison de pannes ou de la gestion des ressources. L’emplacement d’un acteur peut également changer si l’acteur est désactivé au moment où il est appelé.

* *Intégration transparente avec le magasin persistant*. Le modèle de programmation Reliable Actors permet un mappage déclaratif de l’état en mémoire des acteurs vers un magasin persistant. Il synchronise les mises à jour de façon transparente, ce qui garantit que les appelants reçoivent les résultats uniquement après la mise à jour de l'état persistant.

* *Haute disponibilité, prise en charge du basculement et gestion du cycle de vie des applications*. L’état d’un acteur est géré par la plateforme et répliqué de façon à ce qu’il puisse être restauré si, par exemple, un nœud du cluster est défaillant. Service Fabric gère également le cycle de vie des applications et permet des mises à niveau d’application sans temps d’arrêt. Le modèle de programmation Reliable Actors est conçu pour accompagner avec succès les programmeurs dans la mise à l’échelle de leurs applications et services selon plusieurs ordres de grandeur. Ce processus s’effectue en incorporant les meilleurs modèles et pratiques éprouvés et en fournissant une implémentation efficace de fonctionnalités système de niveau inférieur. Voici quelques facteurs clés qui favorisent l’évolutivité et les performances des applications Service Fabric :

  * *Le partitionnement implicite granulaire de l’état de l’application*. En utilisant des acteurs comme des entités directement adressables, les programmeurs décomposent implicitement l’état général de leurs applications. Bien que le modèle de programmation Reliable Actors n’impose pas la taille de l’acteur, il est judicieux dans la plupart des cas de disposer d’un grand nombre d’acteurs (plusieurs millions ou plus). Chacun de ces millions d’acteurs représente une entité naturelle de l’application, par exemple un compte d’utilisateur ou un bon de commande. Comme les acteurs sont adressables individuellement et que leurs emplacements physiques sont gérés par le runtime, il existe une grande flexibilité dans l’équilibrage de la charge et le traitement des zones sensibles. Ces opérations s’effectuent de manière transparente et générique, sans aucune intervention des développeurs d’application.

  * *Gestion adaptative des ressources*. Les acteurs n’émettent aucune hypothèse sur l’emplacement des autres acteurs avec lesquels ils interagissent. En raison de la transparence de ces emplacements, le runtime peut gérer et ajuster l’allocation des ressources matérielles disponibles de façon très dynamique. Pour ce faire, il prend des décisions très précises sur le placement et la migration des acteurs dans l’ensemble du cluster de calcul en réaction à des modèles de charge et de communication, sans faire échouer les demandes entrantes. En créant plusieurs réplicas d’un acteur particulier, le runtime peut augmenter le débit de l’acteur sans apporter de modifications au code de l’application.

  * *Communication multiplexée*. Les acteurs dans Service Fabric disposent de points de terminaison logiques, et la messagerie entre eux est multiplexée sur un ensemble fixe de connexions physiques tous-vers-tous (sockets TCP). Le runtime peut ainsi héberger un très grand nombre (plusieurs millions) d’entités adressables sans aucune surcharge du système d’exploitation par acteur. En outre, l’activation ou la désactivation d’un acteur n’entraîne pas de coût d’inscription ou de désinscription d’un système d’extrémité physique, tel qu’un port TCP ou une URL HTTP.

  * *Planification efficace*. Le runtime Reliable Actors planifie l’exécution des planifications d’un grand nombre d’acteurs à thread unique dans l’ensemble d’un pool de threads personnalisé en utilisant un thread par cœur de processeur physique. Le code de l’acteur étant écrit dans un style de continuation sans blocage (une exigence du modèle de programmation), le code de l’application s’exécute avec un thread unique d’une façon « coopérative » très efficace et sans aucun conflit. Cela permet au système d’atteindre un débit élevé et de s’exécuter avec une très forte sollicitation du processeur (90 % ou plus) et une grande stabilité. La croissance du nombre d’acteurs dans le système et la charge n’entraînent pas de threads supplémentaires ou d’autres primitives de système d’exploitation, ce qui favorise l’évolutivité des nœuds individuels et de l’ensemble du système.

  * *Comportement asynchrone explicite*. Le modèle de programmation Reliable Actors rend explicite la nature asynchrone d’une application distribuée et incite les programmeurs à écrire un code asynchrone non bloquant. Cela garantit un grand degré de parallélisme distribué et un débit global sans utilisation explicite de plusieurs threads.

<!---HONumber=AcomDC_0128_2016-->