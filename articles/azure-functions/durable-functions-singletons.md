---
title: "Singletons pour l’extension Fonctions durables - Azure"
description: "Explique comment utiliser des singletons dans l’extension Fonctions durables pour Azure Functions."
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
ms.openlocfilehash: e82cc53d53a6d0296aaab2c3a76ad4e2f6c12c54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orchestrateurs de singleton dans l’extension Fonctions durables (Azure Functions)

Pour les orchestrations de style acteur ou les travaux en arrière-plan, vous avez souvent besoin de vous assurer qu’une seule instance d’un orchestrateur spécifique soit exécutée. Vous pouvez le vérifier grâce à l’extension [Fonctions durables](durable-functions-overview.md), en affectant un ID d’instance spécifique à un orchestrateur lors de sa création.

## <a name="singleton-example"></a>Exemple de singleton

L’exemple C# suivant illustre une fonction de déclencheur HTTP, qui crée une orchestration singleton de travail en arrière-plan. Il utilise un ID d’instance bien connu pour vérifier qu’il n’existe qu’une seule instance.

```cs
[FunctionName("EnsureSingletonTrigger")]
public static async Task<HttpResponseMessage> Ensure(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    // Ensure only one instance is ever running at a time
    const string OrchestratorName = "MySingletonOrchestrator";
    const string InstanceId = "MySingletonInstanceId";

    var existingInstance = await starter.GetStatusAsync(InstanceId);
    if (existingInstance == null)
    {
        log.Info($"Creating singleton instance with ID = {InstanceId}...");
        await starter.StartNewAsync(OrchestratorName, InstanceId, input: null);
    }

    return starter.CreateCheckStatusResponse(req, InstanceId);
}
```

Par défaut, les ID d’instance sont des identificateurs globaux uniques générés de manière aléatoire. Dans ce cas, vous pouvez noter que la fonction de déclencheur utilise une variable `InstanceId` prédéfinie, avec une valeur de `MySingletonInstanceId`, pour affecter au préalable un ID d’instance à la fonction d’orchestrateur. Cela permet au déclencheur de vérifier si l’instance connue est déjà exécutée, en appelant l’élément [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_).

Les détails liés à l’implémentation de la fonction d’orchestrateur ne sont pas importants. Il peut s’agir d’une fonction d’orchestrateur classique, qui démarre et se termine, ou d’une fonction qui s’exécute sans s’arrêter (une [orchestration externe](durable-functions-eternal-orchestrations.md)). Ce qui importe, c’est qu’une seule instance s’exécute à la fois, systématiquement.

> [!NOTE]
> Si l’instance d’orchestration singleton échoue ou se termine, il ne sera pas possible de la recréer en utilisant le même ID. Dans ce cas, il faudra la recréer avec le nouvel ID d’instance.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’appel d’orchestrations secondaires](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Exécuter un exemple de singleton](durable-functions-counter.md)
