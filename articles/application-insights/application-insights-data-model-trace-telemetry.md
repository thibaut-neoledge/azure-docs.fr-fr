---
title: "Modèle de données de télémétrie d’Azure Application Insights : télémétrie des traces | Microsoft Docs"
description: "Modèle de données Application Insights pour la télémétrie des traces"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: e7825b26c47a80debf92be1ad166e5a18bae4a61
ms.lasthandoff: 04/22/2017


---
# <a name="trace-telemetry-application-insights-data-model"></a>Télémétrie des traces : modèle de données Application Insights

La télémétrie des traces représente les instructions de traces de type `printf` qui sont recherchées dans la zone de texte. `Log4Net`, `NLog` et les autres entrées de fichier journal de type texte sont converties en instances de ce type. La trace ne comporte pas de mesures comme l’extensibilité.

## <a name="message"></a>Message

Message de trace.

Longueur maximale : 32768 caractères

## <a name="severity-level"></a>Niveau de gravité

Niveau de gravité de trace. La valeur peut être `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Propriétés personnalisées

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](/application-insights-data-model.md).
- [Exploration des journaux .NET dans Application Insights](/app-insights-asp-net-trace-logs.md).
- [Exploration du suivi des journaux Java dans Application Insights](/app-insights-java-trace-logs.md).
- Découvrez quelles [plateformes](/app-insights-platforms.md) sont prises en charge par Application Insights.

