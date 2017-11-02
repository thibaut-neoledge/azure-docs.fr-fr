---
title: Orchestrations secondaires pour Fonctions durables - Azure
description: "Guide pratique pour appeler des orchestrations à partir d’orchestrations dans l’extension Fonctions durables pour Azure Functions."
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
ms.openlocfilehash: 5184bef81d1cd6ca7b41c1634def24031a4a5942
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2017
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Orchestrations secondaires dans Fonctions durables (Azure Functions)

En plus d’appeler des fonctions d’activité, les fonctions de l’orchestrateur peuvent appeler d’autres fonctions d’orchestrateur. Par exemple, vous pouvez créer une orchestration plus grande à partir d’une bibliothèque de fonctions d’orchestrateur. Ou vous pouvez exécuter en parallèle plusieurs instances d’une fonction d’orchestrateur.

Une fonction d’orchestrateur peut appeler une autre fonction d’orchestrateur en appelant la méthode [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) ou [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_). L’article [Gestion des erreurs et compensation](durable-functions-error-handling.md#automatic-retry-on-failure) fournit des informations supplémentaires sur la nouvelle tentative automatique.

Les fonctions d’orchestrateur secondaires se comportent comme des fonctions d’activité du point de vue de l’appelant. Elles peuvent retourner une valeur, lever une exception et être attendues par la fonction d’orchestrateur parente.

## <a name="example"></a>Exemple

L’exemple suivant montre un scénario IoT (« Internet of Things ») dans lequel plusieurs appareils doivent être configurés. Une orchestration spécifique doit se produire pour chacun des appareils, ce qui peut se présenter comme suit :

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string deviceId = ctx.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await ctx.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await ctx.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await ctx.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

Cette fonction d’orchestrateur peut être utilisée en l’état pour la configuration unique d’un appareil, ou elle peut faire partie d’une orchestration plus importante. Dans ce cas, la fonction d’orchestrateur parente peut planifier des instances de `DeviceProvisioningOrchestration` à l’aide de l’API `CallSubOrchestratorAsync`.

Voici un exemple montrant comment exécuter en parallèle plusieurs fonctions d’orchestrateur.

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    string[] deviceIds = await ctx.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = ctx.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez ce que représentent les hubs de tâches et comment les configurer](durable-functions-task-hubs.md)
