---
title: "Variables système dans Azure Data Factory | Microsoft Docs"
description: "Cet article décrit les variables système prises en charge par Azure Data Factory. Vous pouvez utiliser ces variables dans des expressions lors de la définition des entités Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: dbf0a12ec1a5348e7c73248cc389ec1a5a5d4a75
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variables système prises en charge par Azure Data Factory
Cet article décrit les variables système prises en charge par Azure Data Factory. Vous pouvez utiliser ces variables dans des expressions lors de la définition des entités Data Factory. 

## <a name="pipeline-scope"></a>Étendue du pipeline :

| Nom de la variable | Description |
| --- | --- |
| @pipeline().DataFactory |Nom de la fabrique de données dans laquelle le pipeline s’exécute | 
| @pipeline().Pipeline |Nom du pipeline |
| @pipeline().RunId | ID de l’exécution du pipeline spécifique | 
| @pipeline().TriggerType | Type de déclencheur qui a appelé le pipeline (manuel, planificateur) | 
| @pipeline().TriggerId| ID du déclencheur qui appelle le pipeline |
| @pipeline().TriggerName| Nom du déclencheur qui appelle le pipeline |
| @pipeline().TriggerTime| Heure à laquelle le déclencheur a appelé le pipeline. L’heure de déclenchement correspond à l’heure de déclenchement réelle, et non à l’heure planifiée. Par exemple, `13:20:08.0149599Z` est retourné au lieu de `13:20:00.00Z` |

## <a name="trigger-scope"></a>Étendue du déclencheur :

| Nom de la variable | Description |
| --- | --- |
| trigger().scheduledTime |Heure à laquelle le déclencheur a été planifié pour appeler l’exécution du pipeline. Par exemple, pour un déclencheur qui se déclenche toutes les 5 min, cette variable retournerait `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z`, respectivement.|
| trigger().startTime |Heure à laquelle le déclencheur a été **effectivement** déclenché pour appeler l’exécution du pipeline. Par exemple, pour un déclencheur qui se déclenche toutes les 5 min, cette variable pourrait retourner des valeurs similaires à `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z`, respectivement.|

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de ces variables dans les expressions, consultez [Langage et fonctions des expressions](control-flow-expression-language-functions.md). 

