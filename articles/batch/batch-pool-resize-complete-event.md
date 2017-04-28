---
title: "Événement de fin de redimensionnement de pool - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: dfee89e3-510f-41a0-ace7-737527f40d20
caps.latest.revision: 4
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 623b6cae00ee0f3b0c5073b1a8bee68d2bf17b72
ms.lasthandoff: 04/13/2017

---
# <a name="pool-resize-complete-event"></a>Événement de fin de redimensionnement de pool
Corps de journal des événements de fin de redimensionnement de pool

## <a name="remarks"></a>Remarques
 Cet événement est émis quand un redimensionnement de pool est terminé ou a échoué.

 L’exemple suivant montre le corps d’un événement de fin de redimensionnement de pool pour un pool dont l’augmentation de la taille s’est terminée correctement.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|Élément|Type|Remarques|
|-------------|----------|-----------|
|id|Chaîne|ID du pool.|
|nodeDeallocationOption|String|Spécifie quand des nœuds peuvent être supprimés du pool en cas de diminution de la taille du pool.<br /><br /> Les valeurs possibles sont les suivantes :<br /><br /> **requeue** : arrêter les tâches en cours d’exécution et les replacer en file d’attente. Les tâches sont ré-exécutées lors de l’activation du travail. Supprimez les nœuds dès que les tâches sont terminées.<br /><br /> **terminate** : mettre fin aux tâches en cours d’exécution. Les tâches ne sont pas ré-exécutées. Supprimez les nœuds dès que les tâches sont terminées.<br /><br /> **taskcompletion** : autoriser l’achèvement des tâches en cours d’exécution. Ne planifiez aucune nouvelle tâche en attendant. Supprimer les nœuds quand toutes les tâches sont terminées.<br /><br /> **Retaineddata** : autoriser l’achèvement des tâches en cours d’exécution, puis attendre que toutes les périodes de rétention des données expirent. Ne planifiez aucune nouvelle tâche en attendant. Supprimez les nœuds une fois que toutes les périodes de rétention ont expiré.<br /><br /> La valeur par défaut est requeue.<br /><br /> Si la taille du pool augmente, cela signifie que la valeur est définie **invalide**.|
|currentDedicated|Int32|Nombre de nœuds de calcul actuellement affectés au pool.|
|targetDedicated|Int32|Nombre de nœuds de calcul demandés pour le pool.|
|enableAutoScale|Bool|Spécifie si la taille du pool s’ajuste automatiquement au fil du temps.|
|isAutoPool|Bool|Spécifie si le pool a été créé via un mécanisme AutoPool du travail.|
|startTime|DateTime|Heure de début du redimensionnement du pool.|
|endTime|DateTime|Heure de fin du redimensionnement du pool.|
|resultCode|String|Résultat du redimensionnement.|
|resultMessage|Chaîne|L’erreur de redimensionnement inclut les détails du résultat.<br /><br /> Si le redimensionnement s’est terminé correctement, indique que l’opération a réussi.|
