---
title: Diagnostics intelligents des modifications des performances des applications web dans Azure Application Insights | Microsoft Docs
description: "Diagnostic automatique des pics d’activité ou des étapes de la télémétrie relative aux performances depuis votre application web."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: bb836357af6d006c18db578164f02fa7bd043b45
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>Diagnostiquer les changements soudains dans la télémétrie de votre application

*Cette fonctionnalité est en version préliminaire.*

Diagnostiquez des changements soudains dans les performances ou l’utilisation de votre application web d’un seul clic ! La fonctionnalité de diagnostics intelligents est disponible dès que vous créez un graphique de temps dans [Analytics](app-insights-analytics.md) dans [Application Insights](app-insights-overview.md). Dès lors qu’une modification soudaine de l’évolution de vos résultats est détectée (un pic ou un creux d’activité, par exemple), Smart Diagnostics identifie un modèle des dimensions et des valeurs connexes pouvant expliquer cette modification. Cela vous permet de diagnostiquer rapidement le problème. 

Dans cet exemple, Smart Diagnostics a identifié un modèle de valeurs de propriété associées à la modification, et met en évidence la différence entre les résultats avec et sans ce modèle :

![exemple de résultat de diagnostic analytique](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>Diagnostiquer les modifications de données

1.    Exécutez une requête dans Analytics et affichez-la sous la forme d’un graphique de temps. 
2.    Cliquez sur l’un des points représentant un pic d’activité, le cas échéant.
 
    ![point représentant un pic d’activité](./media/app-insights-analytics-diagnostics/peak.png)

    Le diagnostic met quelques secondes à identifier un modèle.

3. L’onglet des résultats du diagnostic affiche un modèle pouvant expliquer la discontinuité de vos données.

    ![result](./media/app-insights-analytics-diagnostics/result.png)
 
    Le texte montre les valeurs de dimension qui semblent être en corrélation avec le décalage. Dans cet exemple, il est associé à une requête et à une version de navigateur spécifiques.

    Notez également les deux composants du graphique, avec un filtre true et false. Le composant false indique une tendance inchangée. En d’autres termes, il n’y a aucune modification des résultats de télémétrie, si l’on exclut la combinaison problématique de dimensions identifiée par Diagnostics. En revanche, les résultats de cette combinaison montrent une modification très importante dans la zone d’investigation sélectionnée. Cela indique que Diagnostics a trouvé une combinaison de propriétés expliquant cette modification.

4.    Si le modèle est complexe, vous devrez passer la souris sur **Afficher tout** pour voir les dimensions.

    ![afficher tout](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.    Si Diagnostics ne trouve aucun modèle significatif, la page « aucun résultat » s’affiche. À ce stade, vous pouvez modifier votre requête. Par exemple, vous pouvez limiter l’intervalle de temps et compartimenter la requête Analytics, pour une analyse plus approfondie et obtenir potentiellement de meilleurs résultats.

Maintenant que vous savez qu’une page spécifique de votre site web a un problème sur un navigateur en particulier, vous pouvez désormais accéder directement à la page en question et examiner les modifications récentes.

## <a name="try-the-demo"></a>Essayer la démonstration

[Cliquez ici pour voir une démonstration](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H) sur un échantillon de données.

## <a name="how-it-works"></a>Fonctionnement

Smart Diagnostics utilise un algorithme Machine Learning non supervisé avancé basé sur l’opération [DiffPatterns](app-insights-analytics-reference.md#evaluate-diffpatterns). Il recherche les modèles susceptibles d’expliquer la modification des données. Il analyse l’impact de chaque modèle sur la mesure et affiche le modèle qui correspond le mieux à la modification.

## <a name="no-diagnostic-points"></a>Aucun diagnostic n’est donné ?

Smart Diagnostics fonctionne uniquement lorsque les critères suivants sont réunis :

 * Le paramètre Smart Diagnostics est activé. Recherchez l’icône Paramètres dans Analytics.
 * L’option Smart Diagnostics dans les paramètres d’Analytics est sélectionnée. 
 * Axe du temps : l’axe des abscisses du graphique doit être de type `datetime`.
 * Graphique en courbes ou en aires : Diagnostics fonctionne uniquement avec ces types de graphique. Utilisez `| render timechart` ou `| render areachart` à la fin de votre requête, ou sélectionnez Graphique en courbes ou en aires dans la liste déroulante.
 * Discontinuité : la discontinuité des données doit être significative.
 * Suffisamment de points à analyser.
 * Pas plus d’une clause de synthèse dans la requête.
 * Aucune clause de projet qui contient une définition de nom avant la clause de synthèse.

 
 ## <a name="related-articles"></a>Articles connexes

 * [Didacticiel Analytics](app-insights-analytics-tour.md)
 * [Détection intelligente](app-insights-proactive-diagnostics.md) vous avertit automatiquement en cas de problèmes de performances.
