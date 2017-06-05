---
title: "Analyse de la rétention utilisateur des applications web avec Azure Application Insights | Microsoft Docs"
description: "Combien d’utilisateurs reviennent vers votre application ?"
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 17f6062537714fbef7c8509261ac4875f8a44b6e
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analyse de la rétention utilisateur des applications web avec Azure Application Insights

Le panneau de rétention dans [Azure Application Insights](app-insights-overview.md) vous aide à analyser le nombre d’utilisateurs qui reviennent vers votre application et la fréquence à laquelle ils exécutent des tâches particulières ou atteignent des objectifs. Par exemple, si vous exécutez un site de jeu, vous pouvez comparer le nombre d’utilisateurs qui reviennent sur le site après avoir perdu à un jeu avec le nombre d’utilisateurs qui reviennent après avoir gagné. Cette information peut vous aider à améliorer l’expérience de vos utilisateurs et votre stratégie commerciale.

## <a name="get-started"></a>Prise en main

Si aucune donné n’apparaît dans le panneau de rétention du portail Application Insights, [découvrez comment prendre en main les outils d’utilisation](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>L’outil de rétention

![Outil de rétention](./media/app-insights-usage-retention/retention.png)

a. La page Vue d’ensemble est toujours affichée. Vous pouvez également enregistrer des rapports nommés avec des paramètres différents. Enregistrez sur Partagé pour rendre le rapport accessible aux autres utilisateurs ayant accès à la ressource.

b. Par défaut, le graphique compte tous les utilisateurs ayant utilisé un des événements personnalisés ou des vues de pages reçues de votre application. Sélectionnez-en un, ou un sous-ensemble, pour cibler une activité utilisateur spécifique. 

c. Ajoutez un ou plusieurs filtres sur les propriétés. Par exemple, vous pouvez cibler les utilisateurs dans un pays ou une région spécifique. Cliquez sur **Mettre à jour** après avoir défini les filtres.

d. **Paramètres par défaut** efface toujours le filtre personnalisé et les filtres d’événements.

e. Le graphique de résumé affiche les totaux sur la période sélectionnée.

f. La grille indique le nombre d’utilisateurs qui sont revenus pour répéter les actions sélectionnées sur une période spécifique. Chaque ligne représente une cohorte d’utilisateurs ayant effectué une des actions sélectionnées pendant la période de temps indiquée. Chaque cellule de la ligne indique combien de cette cohorte sont revenus au moins une fois pendant une période ultérieure. Certains utilisateurs peuvent revenir pendant plusieurs périodes.

Quiconque a utilisé l’application pendant l’intervalle de temps du graphique est représenté sur une ligne du graphique. Chaque utilisateur est comptabilisé sur la période lorsqu’il exécute pour la première fois l’action sélectionnée dans l’intervalle de temps du graphique. Le nombre tend ainsi à être plus important dans la première ligne.


## <a name="use-business-events-to-track-retention"></a>Utiliser des événements commerciaux pour suivre la rétention

Pour obtenir l’analyse de rétention la plus utile, mesurez les événements qui représentent des activités commerciales significatives. 

Par exemple, un grand nombre d’utilisateurs peuvent ouvrir une page dans votre application sans jouer au jeu qu’elle affiche. Le suivi des vues de pages uniquement fournit ainsi une estimation inexacte du nombre de personnes qui reviennent pour jouer au jeu auquel ont déjà joué. Pour obtenir une vision claire des lecteurs qui reviennent, votre application doit envoyer un événement personnalisé lorsqu’un utilisateur joue réellement.  

Il est recommandé de coder les événements personnalisés qui représentent des actions commerciales clés et de les utiliser pour votre analyse de rétention. Pour capturer le résultat du jeu, vous devez écrire une ligne de code pour envoyer un événement personnalisé à Application Insights. Si vous l’écrivez dans le code de page web ou en Node.JS, voici à quoi il ressemble :

```JavaScript
    appinsights.trackEvent("won game");
```

Ou dans le code de serveur ASP.NET :

```C#
   telemetry.TrackEvent("won game");
```

[Familiarisez-vous avec l’écriture d’événements personnalisés](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de l’utilisation](app-insights-usage-overview.md)
* [Utilisateurs et sessions](app-insights-usage-segmentation.md)
* [Codage des événements personnalisés](app-insights-api-custom-events-metrics.md)


