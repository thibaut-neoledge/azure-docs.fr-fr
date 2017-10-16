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
ms.author: bwren
ms.openlocfilehash: 207526b7a675f92134d045ebefb9a372749bce92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
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

## <a name="error-pane"></a>Volet d’erreur
Lorsque vous cliquez sur un nœud de la mise en correspondance, un volet d’erreur résumant les échecs de ce nœud s’affiche sur le côté droit. Les échecs sont tout d’abord regroupés par ID d’opération, puis par ID de problème.

![Volet d’erreur](./media/app-insights-app-map/error-pane.png)

Cliquez sur un échec pour accéder à l’instance la plus récente de ce dernier.

## <a name="resource-health"></a>Intégrité des ressources
Pour certains types de ressources, l’intégrité des ressources est indiquée en haut du volet d’erreur. Par exemple, l’intégrité de la base de données et les alertes survenues s’affichent lorsque vous cliquez sur un nœud SQL.

![Intégrité des ressources](./media/app-insights-app-map/resource-health.png)

Vous pouvez cliquer sur le nom de la ressource pour afficher les indicateurs de performances de la vue d’ensemble standard de cette ressource.

## <a name="end-to-end-system-app-maps"></a>Cartes d’applications système de bout en bout

*Requiert le Kit de développement logiciel (SDK) version 2.3 ou ultérieure*

Si votre application possède plusieurs composants (par exemple, un service principal en plus de l’application web), vous pouvez tous les afficher sur une même carte d’applications intégrée.

![Définir les filtres](./media/app-insights-app-map/multi-component-app-map.png)

La mise en correspondance d’applications trouve les nœuds de serveur en suivant les appels de dépendance HTTP effectués entre les serveurs sur lesquels le Kit de développement logiciel (SDK) Application Insights est installé. Chaque ressource Application Insights est supposée contenir un serveur.

### <a name="multi-role-app-map-preview"></a>Mise en correspondance d’applications contenant plusieurs rôles (version préliminaire)

La fonctionnalité de mise en correspondance d’applications contenant plusieurs rôles vous permet d’utiliser la mise en correspondance d’applications avec plusieurs serveurs envoyant des données à la même ressource Application Insights/clé d’instrumentation. Les serveurs de la mise en correspondance sont segmentés par la propriété cloud_RoleName sur les éléments de télémétrie. Définissez la *mise en correspondance d’applications contenant plusieurs rôles* sur *Activé* à partir du panneau Aperçus pour activer cette configuration.

Cette approche peut être souhaitable dans une application de micro-services, ou dans d’autres scénarios où vous souhaitez mettre en corrélation des événements sur plusieurs serveurs au sein d’une seule ressource Application Insights.

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Commentaires
Merci d’envoyer des commentaires via l’option de commentaires du portail.

![Image MapLink-1](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>Étapes suivantes

* [portail Azure](https://portal.azure.com)
