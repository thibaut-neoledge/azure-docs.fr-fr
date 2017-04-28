---
title: "Événement de début de tâche - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 95f7762a-a715-4c83-907b-8aed004e69b1
caps.latest.revision: 3
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 81e27c1db2687c819aee15646c2c72e8765293c8
ms.lasthandoff: 04/13/2017

---
# <a name="task-start-event"></a>Événement de début de tâche
Corps de journal des événements de début de tâche

## <a name="remarks"></a>Remarques
 Cet événement est émis quand une tâche est planifiée pour démarrer sur un nœud de calcul par le Scheduler. Notez que, si la tâche est retentée ou replacée en file d’attente, cet événement est ré-émis pour la même tâche, mais que le nombre de nouvelles tentatives et la version de la tâche système sont mis à jour en conséquence.


 L’exemple suivant montre le corps d’un événement de début de tâche.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "retryCount": 0
    }
}
```

|Nom de l'élément|Type|Remarques|
|------------------|----------|-----------|
|jobId|String|ID du travail contenant la tâche.|
|id|String|ID de la tâche.|
|taskType|Chaîne|Type de la tâche. Ce peut être « JobManager », indiquant qu’il s’agit une tâche du gestionnaire, ou « User », indiquant qu’il ne s’agit pas d’une tâche du gestionnaire.|
|systemTaskVersion|Int32|Compteur de tentatives internes d’exécution d’une tâche. En interne, le service Batch peut recommencer une tâche pour prendre en compte des problèmes temporaires. Ces problèmes peuvent être des erreurs de planification internes ou des tentatives de récupération à partir de nœuds de calcul en mauvais état.|
|[nodeInfo](#nodeInfo)|Type complexe|Contient des informations sur le nœud de calcul sur lequel la tâche a été exécutée.|
|[multiInstanceSettings](#multiInstanceSettings)|Type complexe|Spécifie que la tâche est une tâche multi-instance nécessitant plusieurs nœuds de calcul.  Pour plus d’informations, voir [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task).|
|[constraints](#constraints)|Type complexe|Contraintes d’exécution qui s’appliquent à cette tâche.|
|[executionInfo](#executionInfo)|Type complexe|Contient des informations sur l’exécution de la tâche.|

###  <a name="nodeInfo"></a> nodeInfo

|Nom de l'élément|Type|Remarques|
|------------------|----------|-----------|
|poolId|String|ID u pool sur lequel la tâche a été exécutée.|
|nodeId|String|ID du nœud sur lequel la tâche a été exécutée.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Nom de l'élément|Type|Remarques|
|------------------|----------|-----------|
|numberOfInstances|int|Nombre de nœuds de calcul requis pour la tâche.|

###  <a name="constraints"></a> constraints

|Nom de l'élément|Type|Remarques|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Nombre maximal de fois que la tâche peut être retentée. Le service Batch retente une tâche si le code de sortie de celle-ci diffère de zéro.<br /><br /> Notez que cette valeur contrôle spécifiquement le nombre de nouvelles tentatives. Le service Batch tente la tâche une fois et peut réessayer jusqu’à cette limite. Par exemple, si le nombre maximal de nouvelles tentatives est 3, le service Batch peut tenter d’exécuter la tâche jusqu’à 4 fois (tentative initiale + 3 tentatives supplémentaires).<br /><br /> Si le nombre maximal de tentatives est 0, le service Batch ne réessaye pas d’exécuter des tâches.<br /><br /> Si le nombre maximal de nouvelles tentatives est -1, le service Batch réessaie d’exécuter les tâches sans limite.<br /><br /> La valeur par défaut est 0 (aucune nouvelle tentative).|

###  <a name="executionInfo"></a> executionInfo

|Nom de l'élément|Type|Remarques|
|------------------|----------|-----------|
|retryCount|Int32|Nombre de fois que le service Batch a réessayé d’exécuter la tâche. Si la tâche se termine avec un code de sortie autre que zéro, elle est retentée le nombre de fois spécifié par la valeur MaxTaskRetryCount|

