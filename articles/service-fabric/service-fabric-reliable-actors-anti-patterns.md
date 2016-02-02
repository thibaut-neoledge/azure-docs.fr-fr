<properties
   pageTitle="Quelques anti-modèles Azure Service Fabric Actors | Microsoft Azure"
   description="Quelques pièges potentiels pour les clients qui se familiarisent avec Azure Service Fabric Actors"
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

# Modèle de conception Reliable Actors : quelques anti-modèles

Nous avons identifié les pièges potentiels suivants pour les clients qui se familiarisent avec Azure Service Fabric Reliable Actors :

* Traiter Reliable Actors en tant que système transactionnel. Service Fabric Reliable Actors n’est pas un système de validation à deux phases qui offre un modèle ACID (atomicité, cohérence, isolation et durabilité). Si vous n’implémentez pas la persistance facultative et que l’ordinateur sur lequel l’acteur s’exécute tombe en panne, son état en fait de même. L’acteur se présente très rapidement sur un autre nœud, mais, à moins d’avoir implémenté la persistance de stockage, l’état est perdu. Toutefois, grâce aux nouvelles tentatives, au filtrage des doublons et/ou à la conception idempotent, vous pouvez obtenir un niveau élevé de fiabilité et de cohérence.

* Bloquer. Tout ce que vous faites dans Reliable Actors doit être asynchrone. C’est plutôt facile en général, car les API asynchrones sont nombreuses désormais dans la plateforme Microsoft. Toutefois, si vous devez interagir avec un système qui fournit uniquement une API bloquante, vous devez utiliser un wrapper qui se sert explicitement du pool de threads .NET.

* Sur-construire et laisser l’environnement travailler. Il peut être difficile pour les développeurs habitués à prêter attention aux collections et verrous simultanés, ou qui utilisent des outils pour compiler des objets à partir de XML, de simplement coder une classe qui effectue des opérations simples, comme l’affectation d’une valeur à une variable ou la planification d’un travail. Les tâches planifiées sont intégrées et les verrous ne sont pas nécessaires. L'état n'est pas un ennemi mortel. Pour les développeurs qui ont longtemps travaillé côté serveur dans des environnements à grande échelle, s’habituer peut prendre du temps.

* Utiliser un seul acteur comme goulot d'étranglement. Il est facile de tomber dans ce piège en ayant des millions d’acteurs se déversant dans l’instance unique d’un autre acteur. Utilisez l’approche d’agrégation que nous décrivons dans le [modèle de conception de calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md).

* Mapper des modèles d'entité à l'aveuglette. Cela concerne les développeurs qui viennent d’un univers relationnel, où les problèmes sont modélisés à l’aide d’entités et de leurs relations. Bien que cette approche soit toujours utile pour comprendre le domaine du sujet, elle doit être associée à la pensée orientée service et fusionnée avec le comportement.

<!---HONumber=AcomDC_0121_2016-->