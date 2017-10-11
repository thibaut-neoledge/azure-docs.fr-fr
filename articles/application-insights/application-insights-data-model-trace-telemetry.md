---
title: "Modèle de données de télémétrie d’Azure Application Insights : télémétrie des traces | Microsoft Docs"
description: "Modèle de données Application Insights pour la télémétrie des traces"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: bwren
ms.openlocfilehash: e1da0d6a6fbd9ca5486936c326ade667d7b01006
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2017
---
# <a name="trace-telemetry-application-insights-data-model"></a>Télémétrie des traces : modèle de données Application Insights

La télémétrie des traces (dans [Application Insights](app-insights-overview.md)) représente des instructions de traces de type `printf` qui font l’objet de recherches textuelles. `Log4Net`, `NLog` et les autres entrées de fichier journal de type texte sont converties en instances de ce type. La trace ne comporte pas de mesures comme l’extensibilité.

## <a name="message"></a>Message

Message de trace.

Longueur maximale : 32768 caractères

## <a name="severity-level"></a>Niveau de gravité

Niveau de gravité de trace. La valeur peut être `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Propriétés personnalisées

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Exploration des journaux .NET dans Application Insights](app-insights-asp-net-trace-logs.md).
- [Exploration du suivi des journaux Java dans Application Insights](app-insights-java-trace-logs.md).
- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](application-insights-data-model.md).
- [Écrire des données de télémétrie de trace personnalisées](app-insights-api-custom-events-metrics.md#tracktrace)
- Découvrez quelles [plateformes](app-insights-platforms.md) sont prises en charge par Application Insights.
