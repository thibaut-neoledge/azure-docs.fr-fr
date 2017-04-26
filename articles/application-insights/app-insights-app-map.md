---
title: "Mise en correspondance d’applications dans Azure Application Insights | Microsoft Docs"
description: "Une présentation visuelle des dépendances entre les composants d’application, intégrant des indicateurs de performance clés et des alertes."
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 37b85ba4987f8f29e4e825a17f0a892ddabf9599
ms.lasthandoff: 04/12/2017


---
# <a name="application-map-in-application-insights"></a>Mise en correspondance d’applications dans Application Insights
Dans [Azure Application Insights](app-insights-overview.md), la mise en correspondance d’applications est une présentation visuelle des relations de dépendance des composants de votre application. Chaque composant affiche des indicateurs de performance clés, tels que la charge, les performances, les échecs et les alertes, pour vous aider à détecter n’importe quel composant à l’origine de problèmes de performances ou de défaillances. Vous pouvez cliquer sur n’importe quel composant pour obtenir des diagnostics plus détaillés, par exemple des événements Application Insights. Si votre application utilise les services Azure, vous pouvez également accéder aux diagnostics Azure, et notamment aux recommandations de SQL Database Advisor.

À l’instar des autres graphiques, vous pouvez épingler une mise en correspondance d’applications au tableau de bord Azure, où elle sera entièrement fonctionnelle. 

## <a name="open-the-application-map"></a>Ouvrir la mise en correspondance d’applications
Ouvrir la mise en correspondance à partir du Panneau de vue d’ensemble de votre application :

![ouvrir la mise en correspondance d’applications](./media/app-insights-app-map/01.png)

![mise en correspondance d’applications](./media/app-insights-app-map/02.png)

La mise en correspondance affiche les éléments suivants :

* Tests de disponibilité
* Composant côté client (surveillé avec le Kit de développement logiciel (SDK) JavaScript)
* Composant côté serveur
* Dépendances des composants client et serveur

Vous pouvez développer et réduire les groupes de liens de dépendance :

![réduire](./media/app-insights-app-map/03.png)

Si vous avez de nombreuses dépendances d’un type (SQL, HTTP, etc.), elles peuvent apparaître groupées. 

![dépendances groupées](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>Détecter les problèmes
Chaque nœud possède des indicateurs de performance pertinents, tels que les taux de charge, de performances et de défaillances de ce composant. 

Les icônes d’avertissement mettent en évidence les problèmes éventuels. Un avertissement orange signifie qu’il existe des défaillances dans les requêtes, les vues de page ou les appels de dépendance. Un avertissement rouge signifie un taux de défaillance de plus de 5 %. Si vous souhaitez ajuster ces seuils, ouvrez Options.

![icônes de défaillance](./media/app-insights-app-map/04.png)

En outre, des alertes actives s’affichent : 

![alertes actives](./media/app-insights-app-map/05.png)

Si vous utilisez SQL Azure, une icône vous indique des recommandations éventuelles sur la façon dont vous pouvez améliorer les performances. 

![Recommandation d’Azure](./media/app-insights-app-map/06.png)

Cliquez sur une icône pour obtenir plus de détails :

![Recommandation d’Azure](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>Clics pour le diagnostic
Chacun des nœuds sur la mise en correspondance offre des clics ciblés pour le diagnostic. Les options varient selon le type du nœud.

![options de serveur](./media/app-insights-app-map/09.png)

Pour les composants qui sont hébergés dans Azure, les options incluent des liens directs.

## <a name="filters-and-time-range"></a>Filtres et période
Par défaut, la mise en correspondance récapitule toutes les données disponibles pour la période choisie. Toutefois, vous pouvez filtrer pour inclure uniquement les noms ou les dépendances d’opérations spécifiques.

* Nom de l’opération : cela inclut les vues de pages et les types de demandes côté serveur. Avec cette option, la mise en correspondance affiche l’indicateur de performance clé sur le nœud côté serveur/client pour les opérations sélectionnées uniquement. Elle montre les dépendances appelées dans le contexte de ces opérations spécifiques.
* Nom de base de la dépendance : cela inclut les dépendances du navigateur AJAX et les dépendances côté serveur. Si vous créez un rapport de télémétrie d’une dépendance personnalisée avec l’API TrackDependency, elles s’affichent également ici. Vous pouvez sélectionner les dépendances à afficher sur la mise en correspondance. Actuellement, cette sélection ne filtre pas les demandes côté serveur ou les vues de pages côté client.

![Définir les filtres](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>Enregistrer les filtres
Pour enregistrer les filtres que vous avez appliqués, épinglez la vue filtrée sur un [tableau de bord](app-insights-dashboards.md).

![Épingler au tableau de bord](./media/app-insights-app-map/12.png)

## <a name="end-to-end-system-app-maps"></a>Cartes d’applications système de bout en bout

Si votre application possède plusieurs composants (par exemple, un service principal en plus de l’application web), vous pouvez tous les afficher sur une même carte d’applications intégrée.

![Définir les filtres](./media/app-insights-app-map/multi-component-app-map.png)

La carte d’applications identifie les nœuds serveurs en recherchant toutes les ressources Application Insights dans le groupe de ressources actuel. Elle détecte également les nœuds serveurs en suivant les appels de dépendance suivis par les ressources Application Insights dans le groupe de ressources actuel.


### <a name="setting-up"></a>Configuration

> [!NOTE] 
> La carte d’applications système de bout en bout est en version préliminaire. Vous devez instrumenter vos composants avec une version spéciale du Kit de développement logiciel (SDK) et utiliser une URL spéciale pour voir la carte d’applications. [Découvrez comment configurer les cartes d’applications système de bout en bout](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-app-map-preview.md).

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Commentaires
Merci d’envoyer des commentaires via l’option de commentaires du portail.

![Image MapLink-1](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>Étapes suivantes

* [portail Azure](https://portal.azure.com)
