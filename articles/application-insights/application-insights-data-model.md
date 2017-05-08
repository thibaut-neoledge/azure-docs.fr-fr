---
title: "Modèle de données de télémétrie d’Azure Application Insights | Microsoft Docs"
description: "Présentation du modèle de données Application Insights"
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
ms.openlocfilehash: 52f700bd18da87a9a38e0a791d0ec3496f201e0a
ms.lasthandoff: 04/22/2017


---
# <a name="application-insights-telemetry-data-model"></a>Modèle de données de télémétrie d’Application Insights

Application Insights définit le modèle de données de télémétrie pour la gestion des performances des applications. Ce modèle normalise la collecte des données et permet de créer des scénarios d’analyse indépendants du langage et de la plateforme. Les données collectées par Application Insights modélisent ce schéma standard d’exécution d’application :

![Modèle d’application Application Insights](./media/application-insights-data-model/application-insights-data-model.png)

Il existe deux types d’applications : les applications web (dont le point de terminaison reçoit les ***demandes*** externes) et les applications Webjobs ou Functions (qui se « réveillent » périodiquement pour traiter les données stockées quelque part). Dans les deux cas, nous appelons leur exécution unique une ***opération***. La réussite ou l’échec de l’opération sont signalés par une ***exception***. L’exécution de la logique métier de l’opération peut aussi dépendre d’autres services/stockages. Pour refléter ces concepts, le modèle de données Application Insights définit trois types de télémétrie : [demande](./application-insights-data-model-request-telemetry.md), [exception](/application-insights-data-model-exception-telemetry.md) et [dépendance](/application-insights-data-model-dependency-telemetry.md).

En règle générale, ces types sont définis par l’infrastructure d’application et sont automatiquement collectés par le Kit de développement logiciel (SDK). `ASP.NET MVC` définit la notion d’exécution d’une requête dans son architecture model-view-controller (marque le début et la fin d’une requête). Les appels de dépendance vers SQL sont définis par `System.Data`. Les appels vers les points de terminaison HTTP sont définis par `System.Net`. Vous pouvez étendre les types de données de télémétrie collectées par une plateforme et une infrastructure spécifiques à l’aide de mesures et de propriétés personnalisées. Toutefois, il peut arriver que vous souhaitiez collecter des données de télémétrie personnalisées. Vous pourriez vouloir implémenter une journalisation des diagnostics à l’aide d’une infrastructure d’instrumentation qui vous est familière, comme `Log4Net` ou `System.Diagnostics`. Ou bien, vous pourriez avoir besoin de capturer l’interaction de l’utilisateur avec votre service pour analyser les modèles d’utilisation. Application Insights reconnaît trois types de données supplémentaires qui permettent de modéliser ces scénarios : [trace](/application-insights-data-model-trace-telemetry.md), [événement](/application-insights-data-model-event-telemetry.md) et [mesure](/application-insights-data-model-metric-telemetry.md).

Le modèle de télémétrie d’Application Insights définit la façon dont les données de télémétrie sont [mises en corrélation](/correlation.md) dans l’opération associée. Par exemple, une requête peut appeler une base de données SQL et enregistrer les informations de diagnostic. Vous pouvez définir le contexte de corrélation pour relier ces éléments de télémétrie aux données de télémétrie de la requête.

## <a name="schema-improvements"></a>Améliorations du schéma

Le modèle de données d’Application Insights est certes simple et basique, mais suffisamment puissant pour modéliser les données de télémétrie de votre application. Nous faisons en sorte que ce modèle reste le plus simple et le plus léger possible pour prendre en charge des scénarios essentiels et permettre l’extension du schéma pour une utilisation avancée.

Pour signaler des problèmes concernant le modèle de données ou le schéma et pour faire des suggestions, utilisez le référentiel GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez quelles [plateformes](/app-insights-platforms.md) sont prises en charge par Application Insights.
- Découvrez comment [étendre et filtrer les données de télémétrie](/app-insights-api-filtering-sampling.md).
- Utilisez [l’échantillonnage](/app-insights-sampling.md) pour réduire la quantité de données de télémétrie basées sur le modèle de données.

