---
title: 'Gestion des erreurs dans Fonctions durables : Azure'
description: "Découvrez comment gérer des erreurs dans l’extension Fonctions durables pour Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: ee5362d33bb9dadadb4194457cfd7726f4825f56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Gestion des erreurs dans Fonctions durables (Azure Functions)

Les orchestrations Fonctions durables sont implémentées dans du code et peuvent utiliser les fonctionnalités de gestion des erreurs du langage de programmation. Cela dit, lorsque vous incorporez la gestion des erreurs et les compensations dans vos orchestrations, vous n’avez pas besoin d’apprendre de nouveaux concepts. Toutefois, vous devez tenir compte de certains comportements.

## <a name="errors-in-activity-functions"></a>Erreurs liées aux fonctions d’activité

Toute exception levée dans une fonction d’activité est marshalée vers la fonction d’orchestrateur et levée en tant qu’élément `TaskFailedException`. Vous pouvez écrire du code de gestion des erreurs et de compensation qui correspond le mieux à vos besoins dans la fonction d’orchestrateur.

Par exemple, considérez la fonction d’orchestrateur suivante, qui transfère des fonds d’un compte à un autre :

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        { 
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

Si l’appel à la fonction **CreditAccount** échoue pour le compte de destination, la fonction d’orchestrateur compense cela replaçant les fonds sur le compte source.

## <a name="automatic-retry-on-failure"></a>Nouvelle tentative automatique en cas d’échec

Lorsque vous appelez les fonctions d’activité ou les fonctions de sous-orchestration, vous pouvez spécifier une stratégie de nouvelle tentative automatique. L’exemple suivant tente d’appeler une fonction jusqu’à 3 fois, et attend 5 secondes avant de lancer une nouvelle tentative m:

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions);
    
    // ...
}
```

L’API `CallActivityWithRetryAsync` prend un paramètre `RetryOptions`. Les appels de sous-orchestration utilisant l’API `CallSubOrchestratorWithRetryAsync` peuvent utiliser les mêmes stratégies de nouvelle tentative.

Il existe plusieurs options de personnalisation de la stratégie de nouvelle tentative automatique. Les voici :

* **Nombre maximal de tentatives** : le nombre maximal de nouvelles tentatives.
* **Intervalle avant première nouvelle tentative** : temps d’attente avant la première nouvelle tentative.
* **Coefficient d’interruption** : coefficient permettant de déterminer le taux d’augmentation de l’interruption. La valeur par défaut est de 1.
* **Intervalle maximal entre deux nouvelles tentatives** : durée maximale de l’attente entre deux nouvelles tentatives.
* **Délai de nouvelle tentative** : temps que passe le système à effectuer de nouvelles tentatives. Par défaut, le système effectue ces nouvelles tentatives indéfiniment.
* **Personnalisé** : un rappel défini par l’utilisateur peut être spécifié. Il détermine si un appel de fonction doit être effectué à nouveau.

## <a name="function-timeouts"></a>Délais d’expiration des fonctions

Vous souhaiterez peut-être abandonner un appel de fonction dans une fonction d’orchestration si elle prend trop de temps. Actuellement, la méthode adéquate pour cela consiste à créer un [minuteur durable](durable-functions-timers.md) à l’aide de l’élément `context.CreateTimer`, conjointement avec l’élément `Task.WhenAny`, comme dans l’exemple suivant :

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

## <a name="unhandled-exceptions"></a>Exceptions non prises en charge

Si une fonction d’orchestration échoue avec une exception non prise en charge, les détails de cette dernière sont enregistrés et l’instance s’exécute avec un état `Failed`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Comment diagnostiquer des problèmes](durable-functions-diagnostics.md)
