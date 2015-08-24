<properties
   pageTitle="Quelques anti-modèles Azure Service Fabric Actors"
   description="Quelques pièges potentiels pour les clients qui se familiarisent avec Azure Service Fabric Actors"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2015"
   ms.author="claudioc"/>

# Modèle de conception Acteurs fiables : quelques anti-modèles

Nous avons identifié les pièges potentiels suivants pour les clients qui se familiarisent avec les Acteurs fiables Service Fabric :

* Traiter les Acteurs fiables en tant que système transactionnel. Les Acteurs fiables Service Fabric ne constituent pas un système basé sur deux phases de validation offrant un modèle ACID. Si nous n'implémentons pas la persistance facultative et que l'ordinateur sur lequel l'acteur s'exécute tombe en panne, son état actuel l'accompagne. L'acteur se présente très rapidement sur un autre nœud, mais, à moins d'avoir implémenté la persistance de stockage, l'état est perdu. Toutefois, entre les nouvelles tentatives, le filtrage des doublons et/ou la conception idempotent, vous pouvez obtenir un niveau élevé de fiabilité et de cohérence.

* Bloquer. Tout ce que nous faisons dans Acteurs fiables doit être asynchrone. C'est plutôt facile en général, car les API asynchrones sont nombreuses désormais dans la plateforme Microsoft. Toutefois, si pour une raison quelconque, nous devons interagir avec un système qui fournit uniquement une API bloquante, nous devons utiliser un wrapper qui se sert explicitement du pool de threads .NET.

* Sur-construire. Laissez l'environnement travailler. Il peut être difficile pour les développeurs habitués à prêter attention aux collections et verrous simultanés, ou qui utilisent des outils pour compiler des objets à partir de XML, de simplement coder une classe qui effectue des opérations simples, comme affecter une valeur à une variable ou planifier un travail. Les tâches planifiées sont intégrées. Les verrous sont inutiles. L'état n'est pas un ennemi mortel. Pour ceux qui ont longtemps travaillé côté serveur dans des environnements à grande échelle, il faut du temps pour s'habituer.

* Utiliser un seul acteur comme goulot d'étranglement. Il est souvent trop facile de tomber dans ce piège : avoir des millions d'acteurs se déversant dans l'instance unique d'un autre acteur. Utilisez l'approche d'agrégation que nous avons décrite dans le [modèle de conception de calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md).

* Mapper des modèles d'entité à l'aveuglette. Cela concernent les développeurs qui viennent d'un univers relationnel où les problèmes sont modélisés à l'aide d'entités et de leurs relations. Bien que cette approche soit toujours utile pour comprendre le domaine du sujet, elle doit être associée à la pensée orientée service et fusionnée avec le comportement.

<!---HONumber=August15_HO7-->