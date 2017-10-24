---
title: Diagnostics dans Fonctions durables - Azure
description: "Découvrez comment gérer diagnostiquer les problèmes avec l’extension Fonctions durables pour Azure Functions."
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
ms.openlocfilehash: 5ebab8660dfe21984e1a7f9a1cb925aea60de213
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Diagnostics dans Fonctions durables (Azure Functions)

Il existe plusieurs options permettant de diagnostiquer les problèmes avec [Fonctions durables](durable-functions-overview.md). Certaines de ces options sont les mêmes pour les fonctions standard, et d’autres sont uniques à Fonctions durables.

## <a name="application-insights"></a>Application Insights

[Application Insights](../application-insights/app-insights-overview.md) est la méthode recommandée pour les diagnostics et la surveillance dans Azure Functions. Il en va de même pour Fonctions durables. Pour obtenir une vue d’ensemble montrant comment tirer parti d’Application Insights dans votre application de fonction, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md).

L’extension Fonctions durables d’Azure Functions émet également des *événements de suivi* vous permettent de suivre l’exécution de bout en bout d’une orchestration. Ces événements sont accessibles et interrogés à l’aide de l’outil [Application Insights Analytics](../application-insights/app-insights-analytics.md) dans le portail Azure.

### <a name="tracking-data"></a>Suivi des données

Chaque événement du cycle de vie d’une instance d’orchestration entraîne l’inscription d’un événement de suivi dans la collection des **suivis** d’Application Insights. Cet événement contient une charge utile **customDimensions** avec plusieurs champs.  Les noms de champs contiennent tous le préfixe `prop__`.

* **hubName** : nom du hub de tâches dans lequel vos orchestrations sont en cours d’exécution.
* **appName** : nom de l’application de fonction. Utile si plusieurs de vos applications de fonction partagent la même instance Application Insights.
* **slotName**: [emplacement de déploiement](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) dans lequel s’exécute l’application de fonction actuelle. Utile pour tirer parti des emplacements de déploiement pour la version de vos orchestrations.
* **functionName**: nom de la fonction d’orchestrateur ou d’activité.
* **functionType** : type de la fonction, par exemple **Orchestrateur** or **Activité**.
* **instanceId** : ID unique de l’instance d’orchestration.
* **state** : état d’exécution du cycle de vie de l’instance. Les valeurs valides incluent :
    * **Scheduled** : la fonction a été planifiée pour être exécutée, mais elle n’a pas encore démarré.
    * **Started** : la fonction a démarré, mais elle n’a pas encore été attendue ou s’est terminée.
    * **Awaited** : l’orchestrateur a planifié des tâches et attend qu’elles se terminent.
    * **Listening** : l’orchestrateur écoute une notification d’événement externe.
    * **Completed** : la fonction s’est terminée avec succès.
    * **Failed** : la fonction a échoué avec une erreur.
* **reason**: données supplémentaires associées à l’événement de suivi. Par exemple, si une instance attend une notification d’événement externe, ce champ indique le nom de l’événement attendu. Si une fonction a échoué, il contient les détails de l’erreur.
* **isReplay** : valeur booléenne qui indique si l’événement de suivi est destiné à une réexécution.
* **extensionVersion** : version de l’extension Tâche durable. Ces données sont particulièrement importantes pour signaler d’éventuels bogues dans l’extension. Des instances à long terme peuvent signaler plusieurs versions si une mise à jour se produit pendant leur exécution. 

Le niveau de détail des données de suivi transmises à Application Insights peut être configuré dans la section `logger` du fichier `host.json`.

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

Par défaut, tous les événements de suivi sont transmis. Le volume de données peut être réduit en définissant `Host.Triggers.DurableTask` sur `"Warning"` ou `"Error"`. Dans ce cas, les événements de suivi seront uniquement transmis en cas de situation exceptionnelle.

> [!WARNING]
> Par défaut, les données de télémétrie Application Insights sont échantillonnées par le runtime Azure Functions pour éviter un transfert trop fréquent de données. Cela peut entraîner une perte des informations de suivi si de nombreux événements de cycle de vie se produisent sur une courte période. L’article sur [la surveillance d’Azure Functions](functions-monitoring.md#configure-sampling) explique comment configurer ce comportement.

### <a name="single-instance-query"></a>Requête d’instance unique

La requête suivante affiche les données de suivi historiques d’une seule instance de la fonction d’orchestration [Séquence Hello](durable-functions-sequence.md). Elle s’écrit à l’aide du langage [Application Insights Query Language (AIQL)](https://docs.loganalytics.io/docs/Language-Reference). Elle exclut la réexécution afin que seul le chemin d’exécution *logique* s’affiche.

```AIQL
let targetInstanceId = "bf71335b26564016a93860491aa50c7f";
let start = datetime(2017-09-29T00:00:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| where isReplay == false
| where instanceId == targetInstanceId
| project timestamp, functionName, state, instanceId, appName = cloud_RoleName
```
Le résultat est une liste d’événements indiquant le chemin d’exécution de l’orchestration, y compris toutes les fonctions d’activité.

![Requête Application Insights](media/durable-functions-diagnostics/app-insights-single-instance-query.png)

> [!NOTE]
> Certains de ces événements de suivi peuvent être en désordre en raison du manque de précision dans la colonne `timestamp`. Ce problème est suivi dans GitHub sous la référence [issue #71](https://github.com/Azure/azure-functions-durable-extension/issues/71).

### <a name="instance-summary-query"></a>Requête de résumé de l’instance

La requête suivante affiche l’état de toutes les instances d’orchestration qui ont été exécutées dans un intervalle de temps spécifié.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay == false
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```
Le résultat est une liste d’ID d’instances et leur actuel état d’exécution.

![Requête Application Insights](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Journalisation

Il est important de garder à l’esprit le comportement de réexécution de l’orchestrateur lors de l’écriture des journaux directement à partir d’une fonction d’orchestrateur. Par exemple, considérez la fonction d’orchestrateur suivante :

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```

Les données de journal obtenues se présentent comme suit :

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> N’oubliez pas que même si les journaux annoncent appeler les fonctions F1, F2 et F3, ces fonctions sont uniquement *réellement* appelées la première fois qu’elles sont rencontrées. Les prochains appels qui se produisent lors de la réexécution sont ignorés et les sorties sont réexécutées à la logique d’orchestrateur.

Si vous souhaitez uniquement consigner une non réexécution, vous pouvez écrire une expression conditionnelle qui écrit dans le journal uniquement si `IsReplaying` est `false`. Considérez l’exemple ci-dessus, mais cette fois avec des vérifications de réexécution.

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    if (!ctx.IsReplaying) log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    if (!ctx.IsReplaying) log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    if (!ctx.IsReplaying) log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```
Avec cette modification, la sortie du journal est la suivante :

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="debugging"></a>Débogage

Azure Functions prend directement en charge un code de fonction de débogage, et cette prise en charge s’applique également à Fonctions durables, que ce soit avec une exécution dans Azure ou localement. Toutefois, vous devez tenir compte de certains comportements lors du débogage :

* **Réexécution** : les fonctions d’orchestrateur sont régulièrement réexécutées lors de la réception de nouvelles entrées. Cela signifie qu’une seule exécution *logique* d’une fonction d’orchestrateur peut atteindre le même point d’arrêt plusieurs fois, en particulier si elle est définie très tôt dans le code de la fonction.
* **Await** : chaque fois qu’un événement `await` est rencontré, le contrôle repasse au répartiteur Durable Task Framework. Si c’est la première fois qu’un événement `await` particulier est détecté, la tâche associée n’est *jamais* reprise. Puisque la tâche ne reprend jamais, vous ne pouvez pas *parcourir* l’événement await (F10 dans Visual Studio). Parcourez uniquement des travaux lorsqu’une tâche est réexécutée.
* **Délais d’expiration des messages** : Durable Functions utilise en interne des files d’attente de messages pour gérer l’exécution des fonctions d’orchestrateur et d’activité. Dans un environnement à plusieurs machines virtuelles, si vous arrêtez le débogage pendant de longues périodes, une autre machine virtuelle risque de récupérer le message, ce qui entraîne une double exécution. Ce comportement s’applique également aux fonctions déclenchées par une file d’attente standard, mais il est important de le souligner car les files d’attente constituent un détail d’implémentation.

> [!TIP]
> Lors de la définition de points d’arrêt, si vous voulez uniquement arrêter en cas de non réexécution, vous pouvez définir un point d’arrêt conditionnel qui s’arrête uniquement si `IsReplaying` est `false`.

## <a name="storage"></a>Storage

Par défaut, Durable Functions stocke l’état dans Stockage Azure. Cela signifie que vous pouvez examiner l’état de vos orchestrations à l’aide d’outils tels que l’[Explorateur Stockage Azure Microsoft](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Capture d’écran Explorateur Stockage Azure](media/durable-functions-diagnostics/storage-explorer.png)

Cet outil est utile pour le débogage car il vous permet de visualiser exactement l’état d’une orchestration. Vous pouvez également examiner les messages de files d’attente pour identifier les tâches en attente (ou bloquées dans certains cas).

> [!WARNING]
> Même s’il est pratique d’afficher l’historique d’exécution dans le stockage de table, évitez de créer une dépendance sur cette table. Cette situation peut changer à mesure que l’extension Fonctions durables évolue.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment utiliser les minuteurs durables](durable-functions-timers.md)