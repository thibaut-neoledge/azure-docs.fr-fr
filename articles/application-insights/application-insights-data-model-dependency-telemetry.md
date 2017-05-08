---
title: "Modèle de données de télémétrie d’Azure Application Insights : télémétrie des dépendances | Microsoft Docs"
description: "Modèle de données Application Insights pour la télémétrie des dépendances"
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
ms.openlocfilehash: b86b1e1ff7c0370918b2509f98ad65265eb4186e
ms.lasthandoff: 04/22/2017


---
# <a name="dependency-telemetry-application-insights-data-model"></a>Télémétrie des dépendances : modèle de données Application Insights

La télémétrie des dépendances représente une interaction entre un composant surveillé et un composant distant tel que SQL ou un point de terminaison HTTP.

## <a name="name"></a>Nom

Nom de la commande lancée par cet appel de dépendance. Valeur de faible cardinalité. Exemples : nom de procédure stockée et modèle de chemin d’accès d’URL.

## <a name="id"></a>ID

Identificateur d’une instance d’appel de dépendance. Utilisé pour la corrélation avec l’élément de télémétrie de demande correspondant à cet appel de dépendance. Pour plus d’informations, consultez la page relative à la [corrélation](/correlation.md).

## <a name="data"></a>Données

Commande lancée par cet appel de dépendance. Exemples : instruction SQL et URL HTTP avec tous les paramètres de requête.

## <a name="type"></a>Type

Nom du type de dépendance. Valeur de faible cardinalité pour le regroupement logique de dépendances et l’interprétation d’autres champs comme commandName et resultCode. Exemples : SQL, table Azure et HTTP.

## <a name="target"></a>Cible

Site cible d’un appel de dépendance. Exemples : nom de serveur, adresse d’hôte. Pour plus d’informations, consultez la page relative à la [corrélation](/correlation.md).

## <a name="duration"></a>Durée

Durée de la demande au format : `DD.HH:MM:SS.MMMMMM`. Doit être inférieure à `1000` jours.

## <a name="result-code"></a>Code de résultat

Code de résultat d’un appel de dépendance. Exemples : code d’erreur SQL et code d’état HTTP.

## <a name="success"></a>Succès

Indication de la réussite ou non d’un appel.

## <a name="custom-properties"></a>Propriétés personnalisées

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Mesures personnalisées

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](/application-insights-data-model.md).
- Configurez le suivi des dépendances pour [.NET](/app-insights-asp-net-dependencies.md).
- Configurez le suivi des dépendances pour [Java](/app-insights-java-agent.md).
- Découvrez quelles [plateformes](/app-insights-platforms.md) sont prises en charge par Application Insights.

