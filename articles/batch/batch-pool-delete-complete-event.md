---
title: "Événement de fin de suppression de pool Azure Batch | Microsoft Docs"
description: "Référence pour l’événement de fin de suppression de pool Batch."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 890f2ba7fda37060c56177868d6214d517d91831
ms.lasthandoff: 04/22/2017

---

# <a name="pool-delete-complete-event"></a>Événement de fin de suppression de pool

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
