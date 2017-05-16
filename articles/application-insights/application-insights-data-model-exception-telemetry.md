---
title: "Modèle de données de télémétrie d’Azure Application Insights : télémétrie des exceptions | Microsoft Docs"
description: "Modèle de données Application Insights pour la télémétrie des exceptions"
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
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 49d2429c01fe3fde2e51e4344904f55e43eea1cd
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


---
# <a name="exception-telemetry-application-insights-data-model"></a>Télémétrie des exceptions : modèle de données Application Insights

Dans [Application Insights](app-insights-overview.md), une instance d’exception représente une exception prise en charge ou non prise en charge générée pendant l’exécution de l’application surveillée.

## <a name="problem-id"></a>ID du problème

Identificateur indiquant à quel endroit du code l’exception a été levée. Utilisé pour le regroupement des exceptions. Généralement, une combinaison de type d’exception et une fonction de la pile des appels.

Longueur maximale : 1024 caractères

## <a name="severity-level"></a>Niveau de gravité

Niveau de gravité de trace. La valeur peut être `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Détails de l’exception

(À développer)

## <a name="custom-properties"></a>Propriétés personnalisées

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Mesures personnalisées

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](application-insights-data-model.md).
- Découvrez comment [diagnostiquer les exceptions dans vos applications web avec Application Insights](app-insights-asp-net-exceptions.md).
- Découvrez quelles [plateformes](app-insights-platforms.md) sont prises en charge par Application Insights.

