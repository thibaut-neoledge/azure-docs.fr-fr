---
title: Bonnes pratiques pour Azure Functions | Microsoft Docs
description: "Découvrez les bonnes pratiques et les modèles pour Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "azure functions, modèles, bonne pratique, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/09/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: 4544629c47326d448cd99b5d96d79666a56f0274
ms.openlocfilehash: 04a5e190a69b1a1a4d0fe6c49c16ddb15593ba38

---

# <a name="best-practices-for-azure-functions"></a>Bonnes pratiques pour Azure Functions

##<a name="overview"></a>Vue d'ensemble

Cet article fournit un ensemble de bonnes pratiques à prendre en compte durant l’implémentation des applications de fonction. Gardez à l’esprit que votre application de fonction Azure est un service d’application Azure. Ainsi, pensez à suivre ces bonnes pratiques.  


## <a name="avoid-large-long-running-functions"></a>Éviter les fonctions volumineuses dont l’exécution prend beaucoup de longtemps

Ces fonctions peuvent provoquer des problèmes de délai d’attente inattendus. Une fonction peut être volumineuse en raison des nombreuses dépendances Node.js. L’importation de ces dépendances peut entraîner une augmentation des temps de chargement aboutissant à des délais d’attente inattendus. Les dépendances Node.js peuvent être chargées explicitement par plusieurs instructions `require()` dans votre code. Elles peuvent également être implicites, en fonction d’un module spécifique chargé par votre code possédant ses propres dépendances internes.  

Chaque fois que possible, subdivisez les fonctions volumineuses en ensembles de fonctions plus petits qui collaborent et retournent des réponses rapides. Par exemple, un webhook ou une fonction de déclenchement HTTP peut nécessiter une réponse avec accusé de réception dans un délai imparti. Vous pouvez passer la charge utile du déclencheur HTTP dans une file d’attente en vue de son traitement par une fonction de déclenchement de file d’attente. Cette approche vous permet de différer le travail réel et de retourner une réponse immédiate. Il est courant que les webhooks demandent une réponse immédiate.


## <a name="cross-function-communication"></a>Communication entre fonctions

Quand vous intégrez plusieurs fonctions, une bonne pratique consiste généralement à utiliser des files d’attente de stockage pour la communication entre les fonctions.  La principale raison est que les files d’attente de stockage sont plus économiques et beaucoup plus faciles à configurer. 

La taille de chaque message d’une file d’attente de stockage est limitée à 64 Ko. Pour transmettre des messages plus volumineux entre les fonctions, vous pouvez utiliser une file d’attente Azure Service Bus, qui prend en charge les tailles de message allant jusqu’à 256 Ko.

Les rubriques Service Bus sont utiles si vous avez besoin de filtrer les messages avant de les traiter.

Les hubs d’événements sont utiles pour prendre en charge les communications de volume élevé.



## <a name="write-functions-to-be-stateless"></a>Écrire des fonctions sans état 

Les fonctions doivent être sans état et idempotentes si possible. Associez toutes les informations d’état requises à vos données. Par exemple, une commande en cours de traitement aurait probablement un membre `state` associé. Une fonction peut traiter une commande suivant cet état, tandis que la fonction elle-même reste sans état. 

Les fonctions idempotentes sont particulièrement recommandées avec les déclencheurs à minuterie. Par exemple, si une tâche doit absolument s’exécuter une fois par jour, écrivez-la de façon à ce qu’elle puisse s’exécuter à n’importe quel moment de la journée avec les mêmes résultats. La fonction peut s’arrêter si aucun travail ne doit être effectué au cours d’un jour donné. En outre, si une exécution précédente a été interrompue, la prochaine exécution doit reprendre au point d’interruption.


## <a name="write-defensive-functions"></a>Écrire des fonctions défensives

Supposons que votre fonction peut être confrontée à une exception à tout moment. Concevez vos fonctions de façon à ce qu’elles puissent reprendre à un point d’échec précédent la prochaine fois qu’elles s’exécutent. Imaginez un scénario qui nécessite les actions suivantes :

1. Récupérer 10 000 lignes d’une base de données.
2. Créer un message de file d’attente pour chacune de ces lignes en vue de leur traitement.
 
Selon la complexité de votre système, il est possible que des services impliqués en aval se comportent de manière incorrecte, que des pannes réseau se produisent, que des limites de quota soient atteintes, etc. Tous ces facteurs peuvent affecter votre fonction à tout moment. Vous devez concevoir vos fonctions en conséquence.

Comment votre code réagit-il si une défaillance se produit après l’insertion de 5 000 de ces éléments dans une file d’attente en vue de leur traitement ? Suivez les éléments dans un jeu que vous avez terminé. Sinon, vous pouvez les réinsérer la prochaine fois. Cela peut avoir un impact sérieux sur votre flux de travail. 

Si un élément de file d’attente a déjà été traité, permettez à votre fonction d’être une absence d’opération.

Tirez parti des mesures défensives déjà fournies pour les composants que vous utilisez dans la plateforme Azure Functions. Par exemple, consultez **Gestion des messages de file d’attente incohérents** dans la documentation sur les [déclencheurs de file d’attente Stockage Azure](functions-bindings-storage.md#storagequeuetrigger).
 



## <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Ne pas mélanger code de test et code de production dans la même application de fonction

Les fonctions d’une application de fonction partagent des ressources. Par exemple, la mémoire est partagée. Si vous utilisez une application de fonction en production, n’y ajoutez pas de ressources et de fonctions de test. Cela peut entraîner une surcharge inattendue pendant l’exécution du code de production.

Soyez attentif à ce que vous chargez dans vos applications de fonction en production. La mémoire moyenne est calculée pour chaque fonction au sein de l’application.

Si un assembly partagé est référencé dans plusieurs fonctions .Net, placez-le dans un dossier partagé commun. Référencez l’assembly avec une instruction similaire à l’exemple suivant : 

    #r "..\Shared\MyAssembly.dll". 

Sinon, il est facile de déployer accidentellement plusieurs versions de test du même binaire qui se comportent différemment d’une fonction à l’autre.

N’utilisez pas de journalisation détaillée dans le code de production. Cela affecte les performances.



## <a name="use-async-code-but-avoid-taskresult"></a>Utiliser du code asynchrone tout en évitant Task.Result

La programmation asynchrone est une pratique recommandée. Toutefois, évitez toujours de référencer la propriété `Task.Result`. Cette approche effectue essentiellement une attente active sur un verrou d’un autre thread. Le maintien d’un verrou peut donner lieu à des blocages.




## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Informations de référence pour les développeurs C# sur Azure Functions](functions-reference-csharp.md)
* [Informations de référence pour les développeurs F# sur Azure Functions](functions-reference-fsharp.md)
* [Azure Functions NodeJS developer reference (Référence pour les développeurs NodeJS Azure Functions)](functions-reference-node.md)




<!--HONumber=Nov16_HO3-->


