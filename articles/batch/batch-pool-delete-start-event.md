---
title: "Événement de début de suppression de pool - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: feddca1e-c69c-4257-be44-a36172e77157
caps.latest.revision: 4
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: ef61238c6dc342173215ae628565a0b87a86bcad
ms.lasthandoff: 04/13/2017

---
# <a name="pool-delete-start-event"></a>Événement de début de suppression de pool
Corps de journal des événements de début de suppression de pool

## <a name="remarks"></a>Remarques
 Cet événement est émis quand une opération de suppression du pool a commencé. Étant donné que la suppression de pool est un événement asynchrone, vous pouvez vous attendre à ce qu’un événement de fin de suppression de pool soit émis au terme de l’opération de suppression.

 L’exemple suivant montre le corps d’un événement de début de suppression de pool.

```
{
    "id": "myPool1"
}
```

|Élément|Type|Remarques|
|-------------|----------|-----------|
|id|Chaîne|ID du pool.|
