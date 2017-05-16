---
title: "Modèle de données de télémétrie d’Azure Application Insights | Microsoft Docs"
description: "Présentation du modèle de données Application Insights"
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
ms.openlocfilehash: 7dd240c4e1a6fcc9c89bf4418e635e7ef8ef0617
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


---
# <a name="application-insights-telemetry-data-model"></a>Modèle de données de télémétrie d’Application Insights

[Azure Application Insights](app-insights-overview.md) envoie la télémétrie à partir de votre application web sur le portail Azure afin que vous puissiez analyser les performances et l’utilisation de votre application. Le modèle de télémétrie est normalisé afin qu’il soit possible de créer une plateforme et une surveillance indépendante du langage. 

Les données collectées par Application Insights modélisent ce schéma standard d’exécution d’application :

![Modèle d’application Application Insights](./media/application-insights-data-model/application-insights-data-model.png)

Les types de télémétrie suivants sont utilisés pour surveiller l’exécution de votre application. Les trois types suivants sont généralement collectés automatiquement par le Kit de développement logiciel (SDK) Application Insights à partir de l’infrastructure d’application web :

* [**Demande** ](application-insights-data-model-request-telemetry.md) : généré pour enregistrer une demande reçue par votre application. Par exemple, le Kit de développement logiciel (SDK) web Application Insights génère automatiquement un élément de télémétrie de demande pour chaque demande HTTP reçue par votre application web. 

    Une **opération** désigne le thread d’exécution qui traite une demande. Vous pouvez également [écrire du code](app-insights-api-custom-events-metrics.md#trackrequest) pour surveiller d’autres types d’opération, comme une « sortie de veille » dans une tâche ou une fonction web qui traite périodiquement des données.  Chaque opération a un ID qui peut être utilisé pour regrouper d’autres données de télémétrie générées lors du traitement de la demande par votre application. Chaque opération réussit ou échoue et a une certaine durée.
* [**Exception**](application-insights-data-model-exception-telemetry.md) : représente normalement une exception qui provoque l’échec d’une opération.
* [**Dépendance** ](application-insights-data-model-dependency-telemetry.md) : représente un appel depuis votre application vers un service ou un stockage externe, comme une API REST ou SQL. Dans ASP.NET, les appels de dépendance vers SQL sont définis par `System.Data`. Les appels vers les points de terminaison HTTP sont définis par `System.Net`. 

Application Insights fournit trois types de données supplémentaires pour la télémétrie personnalisée :

* [Trace](application-insights-data-model-trace-telemetry.md) : utilisé directement ou via un adaptateur pour implémenter la journalisation des diagnostics à l’aide d’une infrastructure d’instrumentation qui vous est familière, comme `Log4Net` ou `System.Diagnostics`.
* [Événement](application-insights-data-model-event-telemetry.md) : normalement utilisé pour capturer l’interaction de l’utilisateur avec votre service afin d’analyser les modèles d’utilisation.
* [Métrique](application-insights-data-model-metric-telemetry.md) : utilisé pour générer des rapports sur les mesures scalaires périodiques.

Le modèle de télémétrie d’Application Insights définit un moyen de [mettre en corrélation](application-insights-correlation.md) la télémétrie et l’opération dont elle fait partie. Par exemple, une requête peut appeler une base de données SQL et enregistrer les informations de diagnostic. Vous pouvez définir le contexte de corrélation pour relier ces éléments de télémétrie aux données de télémétrie de la requête.

## <a name="schema-improvements"></a>Améliorations du schéma

Le modèle de données d’Application Insights est certes simple et basique, mais suffisamment puissant pour modéliser les données de télémétrie de votre application. Nous faisons en sorte que ce modèle reste le plus simple et le plus léger possible pour prendre en charge des scénarios essentiels et permettre l’extension du schéma pour une utilisation avancée.

Pour signaler des problèmes concernant le modèle de données ou le schéma et pour faire des suggestions, utilisez le référentiel GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema).

## <a name="next-steps"></a>Étapes suivantes

- [Écrire des données de télémétrie personnalisées](app-insights-api-custom-events-metrics.md)
- Découvrez comment [étendre et filtrer la télémétrie](app-insights-api-filtering-sampling.md).
- Utilisez [l’échantillonnage](app-insights-sampling.md) pour réduire la quantité de données de télémétrie basées sur le modèle de données.
- Découvrez quelles [plateformes](app-insights-platforms.md) sont prises en charge par Application Insights.

