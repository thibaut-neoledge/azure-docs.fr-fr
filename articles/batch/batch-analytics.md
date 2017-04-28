---
title: Analyse en mode batch - Azure | Microsoft Docs
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 2fda4d9c-f782-4088-9320-656b450e3100
caps.latest.revision: 7
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 4b73637e0ec6798b701d5a13f96c504bcbb14cc7
ms.lasthandoff: 04/13/2017

---
# <a name="batch-analytics"></a>Analyse en mode batch
Les rubriques relatives à l’analyse en mode batch contiennent des informations de référence concernant les événements et alertes disponibles pour les ressources de service Batch.

Pour plus d’informations sur l’activation et l’utilisation des journaux de diagnostic de Batch, voir [Journalisation des diagnostics Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/).

## <a name="diagnostic-logs"></a>Journaux de diagnostic

Le service Microsoft Azure Batch émet les événements de journal de diagnostic suivants pendant la durée de vie de certaines ressources Batch.

**Événements du journal de service**
* [Création de pool](batch-pool-create-event.md)
* [Début de suppression de pool](batch-pool-delete-start-event.md)
* [Fin de suppression de pool](batch-pool-delete-complete-event.md)
* [Début de redimensionnement de pool](batch-pool-resize-start-event.md)
* [Fin de redimensionnement de pool](batch-pool-resize-complete-event.md)
* [Début de tâche](batch-task-start-event.md)
* [Fin de tâche](batch-task-complete-event.md)
* [Échec de tâche](batch-task-fail-event.md)
