---
title: "Événement de fin de suppression de pool - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 580a1278-5740-4143-826c-7d875ef127ff
caps.latest.revision: 5
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 991ee552ccc564be149844b58a0a3fea7452f2a3
ms.lasthandoff: 04/13/2017

---
# <a name="pool-delete-complete-event"></a>Événement de fin de suppression de pool
Corps de journal des événements de fin de suppression de pool

## <a name="remarks"></a>Remarques
 Cet événement est émis quand une opération de suppression de pool est terminée.

 L’exemple suivant montre le corps d’un événement de fin de suppression de pool.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Élément|Type|Remarques|
|-------------|----------|-----------|
|id|String|ID du pool.|
|startTime|DateTime|Heure de début de la suppression du pool.|
|endTime|DateTime|Heure de fin de la suppression du pool.|

## <a name="remarks"></a>Remarques
Pour plus d’informations sur les états et les codes d’erreur pour l’opération de redimensionnement de pool, voir [Supprimer un pool d’un compte](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
