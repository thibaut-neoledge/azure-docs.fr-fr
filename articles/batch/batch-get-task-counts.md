---
title: "Surveiller la progression d’un travail en comptant les tâches par état - Azure Batch | Microsoft Docs"
description: "Surveiller la progression d’un travail en appelant l’opération Obtenir le nombre de tâches pour compter les tâches d’un travail. Vous pouvez obtenir un nombre de tâches actives, en cours d’exécution et terminées et selon que les tâches ont réussi ou échoué."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: tamram
ms.openlocfilehash: ceff59d7063b60a1344a47489d3d73e0e8ee07df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>Compter les tâches par état pour surveiller la progression d’un travail (préversion)

Azure Batch fournit un moyen efficace pour surveiller la progression d’un travail quand il exécute ses tâches. Vous pouvez appeler l’opération [Obtenir le nombre de tâches][rest_get_task_counts] pour déterminer le nombre de tâches par état (active, en cours d’exécution ou terminée), ainsi que le nombre de tâches ayant réussi ou échoué. En comptant le nombre de tâches dans chaque état, vous pouvez plus facilement afficher la progression du travail à un utilisateur ou détecter des retards ou échecs inattendus susceptibles d’affecter le travail.

> [!IMPORTANT]
> L’opération Obtenir le nombre de tâches est en préversion et n’est pas encore disponible dans Azure Government, Azure Chine et Azure Allemagne. 
>
>

## <a name="how-tasks-are-counted"></a>Comptage des tâches

L’opération Obtenir le nombre de tâches compte les tâches par état, comme suit :

- Une tâche est comptée comme **active** quand elle est en file d’attente et en mesure de s’exécuter, mais qu’elle n’est pas encore affectée à un nœud de calcul. Une tâche est également comptée comme **active** si elle dépend d’une tâche parente qui n’est pas encore terminée. Pour plus d’informations sur les dépendances de tâches, consultez [Créer des dépendances de tâches pour exécuter des tâches qui dépendent d’autres tâches](batch-task-dependencies.md). 
- Une tâche est comptée comme **en cours d’exécution** quand elle a été affectée à un nœud de calcul, mais qu’elle n’est pas encore terminée. Une tâche est comptée comme **en cours d’exécution** quand son état est `preparing` ou `running`, comme indiqué par l’opération [Obtenir des informations sur une tâche][rest_get_task].
- Une tâche est comptée comme **terminée** quand elle n’est plus habilitée à s’exécuter. Une tâche comptée comme **terminée** est une tâche qui s’est généralement déroulée correctement ou qui a échoué et a épuisé par ailleurs son nombre maximal de tentatives. 

L’opération Obtenir le nombre de tâches indique également le nombre de tâches ayant réussi ou échoué. Le service Batch détermine si une tâche a réussi ou échoué en vérifiant la propriété **result** de la propriété [executionInfo][https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo] :

    - Une tâche est comptée comme **ayant réussi** si le résultat de l’exécution de la tâche est `success`.
    - Une tâche est comptée comme **ayant échoué** si le résultat de l’exécution de la tâche est `failure`.

Pour plus d’informations sur les états des tâches, consultez [Obtenir des informations sur une tâche][rest_get_task].

L’exemple de code .NET suivant montre comment récupérer le nombre de tâches par état : 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> Pour obtenir le nombre de tâches pour un travail, vous pouvez utiliser un modèle semblable pour REST et d’autres langages pris en charge. 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>Vérification de la cohérence des nombres de tâches

Le service Batch agrège les nombres de tâches en collectant des données à partir de plusieurs composants d’un système distribué asynchrone. Pour que les nombres de tâches soient corrects, le service Batch fournit une validation supplémentaire pour les nombres d’états en effectuant des vérifications de cohérence par rapport à plusieurs composants du système. Le service Batch effectue ces vérifications de cohérence tant que le travail comporte moins de 200 000 tâches. Dans l’hypothèse peu probable où la vérification de cohérence trouverait des erreurs, le service Batch corrige le résultat de l’opération Obtenir le nombre de tâches en fonction des résultats de la vérification de cohérence. La vérification de cohérence est une mesure supplémentaire grâce à laquelle les clients qui s’appuient sur l’opération Obtenir le nombre de tâches reçoivent les informations adéquates nécessaires à leur solution.

La propriété **validationStatus** dans la réponse indique si le service Batch a effectué la vérification de cohérence. Si le service Batch n’a pas pu vérifier les nombres d’états par rapport aux états réels contenus dans le système, la propriété **validationStatus** est définie sur `unvalidated`. Pour des raisons de performances, le service Batch n’effectue pas la vérification de cohérence si le travail inclut plus de 200 000 tâches ; dans ce cas, la propriété **validationStatus** peut être définie sur `unvalidated`. Toutefois, le nombre de tâches n’est pas nécessairement incorrect dans ce cas, car même une perte de données très limitée est très improbable. 

Quand une tâche change d’état, le pipeline d’agrégation traite le changement en quelques secondes. L’opération Obtenir le nombre de tâches reflète le nombre de tâches mises à jour pendant cette période. Toutefois, si le pipeline d’agrégation ne détecte pas un changement de l’état d’une tâche, ce changement n’est pas enregistré avant la prochaine phase de validation. Pendant cette période, les nombres de tâches peuvent être légèrement erronés en raison de l’événement manqué, mais ils sont corrigés à la prochaine phase de validation.

## <a name="best-practices-for-counting-a-jobs-tasks"></a>Bonnes pratiques pour le comptage des tâches d’un travail

Appeler l’opération Obtenir le nombre de tâches est le moyen le plus efficace de retourner un nombre de base des tâches d’un travail par état. Si vous utilisez la version 2017-06-01.5.1 du service Batch, nous vous recommandons d’écrire ou de mettre à jour votre code pour utiliser l’opération Obtenir le nombre de tâches.

L’opération Obtenir le nombre de tâches n’est pas disponible dans les versions du service Batch antérieures à 2017-06-01.5.1. Si vous utilisez une version antérieure du service, vous devez utiliser une requête de liste pour compter les tâches d’un travail. Pour plus d’informations, consultez [Créer des requêtes pour répertorier les ressources Batch efficacement](batch-efficient-list-queries.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les concepts et fonctionnalités du service Batch, consultez [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md). L’article traite des principales ressources Batch, notamment des pools, des nœuds de calcul, des travaux et des tâches et fournit une vue d’ensemble des fonctionnalités du service.
* Découvrez les bases du développement d’une application Batch à l’aide de la [bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md) ou [Python](batch-python-tutorial.md). Cette présentation vous décrit la création d’une application opérationnelle qui utilise le service Batch pour exécuter une charge de travail sur plusieurs nœuds de calcul.


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job
