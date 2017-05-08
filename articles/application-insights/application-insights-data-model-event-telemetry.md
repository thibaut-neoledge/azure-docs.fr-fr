---
title: "Modèle de données de télémétrie d’Azure Application Insights : télémétrie des événements | Microsoft Docs"
description: "Modèle de données Application Insights pour la télémétrie des événements"
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
ms.openlocfilehash: 5b8b803f26dca82b5112568f486e5c347602a409
ms.lasthandoff: 04/22/2017


---
# <a name="event-telemetry-application-insights-data-model"></a>Télémétrie des événements : modèle de données Application Insights

Les événements représentent une action dans le temps qui s’est produite dans l’application. En général, il s’agit d’une interaction utilisateur comme un clic sur un bouton ou une validation de commande. Il peut aussi s’agir d’un événement lié au cycle de vie de l’application, comme une initialisation ou une mise à jour de configuration. Le nom de l’événement doit être une chaîne courte de faible cardinalité. 

Sur le plan sémantique, les événements ne sont pas forcément corrélés aux requêtes. Toutefois, si la télémétrie des événements est utilisée correctement, elle est plus importante que les requêtes ou les traces. Les événements représentent la télémétrie métier et doivent faire l’objet d’un échantillonnage distinct, moins agressif.

## <a name="name"></a>Nom

Nom de l’événement. Ils doivent conserver une cardinalité faible pour permettre un regroupement adéquat et des mesures utiles.

Longueur maximale : 512 caractères

## <a name="custom-properties"></a>Propriétés personnalisées

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Mesures personnalisées

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](/application-insights-data-model.md).
- Découvrez comment utiliser [l’API Application Insights pour les événements et les mesures personnalisés](/app-insights-asp-net-dependencies.md).
- Découvrez quelles [plateformes](/app-insights-platforms.md) sont prises en charge par Application Insights.

