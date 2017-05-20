---
title: "Métriques temps réel et diagnostics personnalisés dans Azure Application Insights | Microsoft Docs"
description: "Surveillez votre application web en temps réel avec des métriques personnalisées, et diagnostiquez les problèmes avec un flux temps réel des échecs, traces et événements."
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 86e01cf6cb14334e85da4102610fa7feb66cb543
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="custom-live-metrics-and-events-monitor--diagnose-with-1-second-latency"></a>Métriques temps réel et événements personnalisés : surveiller et diagnostiquer avec une latence de 1 seconde 
Le Flux de métriques temps réel vous montre vos mesures et événements [Application Insights](app-insights-overview.md) avec une latence d’une seconde, proche du temps réel. Cette surveillance instantanée permet de réduire le temps moyen pour détecter et diagnostiquer et vous aide à respecter les contrats de niveau de service (SLA). Vous pouvez :
* Surveiller en temps réel des indicateurs de performance clés personnalisés : expérimentez avec des filtres, en segmentant et en traitant vos données de télémétrie Application Insights existantes afin d’obtenir instantanément les indicateurs de performance clés les plus pertinents à partir du portail. Aucun code, changement de configuration ou déploiement nécessaire. Des métriques ou mesures personnalisées qui peuvent vous être envoyées sont également disponibles.
* Détecter et diagnostiquer en temps réel : identifiez les échecs de demande et de dépendance lorsqu’ils surviennent, ainsi que les traces d’exception détaillées. Éliminez les problèmes connus pour vous concentrer sur des problèmes réels/nouveaux.
* Débogage en temps réel : reproduisez un problème et consultez toutes les données de télémétrie liées en temps réel, grâce à un filtrage personnalisé sur un ID de session spécifique (ou des attributs personnalisés) qui identifie l’interaction de reproduction. Collectez des informations sur un/tous les serveurs pour gagner du temps dans la résolution du problème.
* Voir instantanément les réactions de votre consommation de ressources à la charge : surveillez *des* compteurs de performances Windows en temps réel, à mesure que vous exécutez des tests de charge, ou surveillez la production pour agir avant tout impact. Aucun changement de configuration ou déploiement nécessaire.
* Validez un correctif publié. Vérifiez le bon fonctionnement de l’exécution de la mise à jour de votre service. Vérifiez que l’échec que vous avez corrigé ne se produit plus.
* Identifiez plus facilement un serveur qui rencontre des problèmes et filtrez tous les indicateurs de performance clés/flux en temps réel sur ce serveur uniquement.

Le flux de métriques et événements temps réel est gratuit : il ne s’ajoute pas à votre facture. Les données sont diffusées à partir de vos serveurs à la demande lorsque vous ouvrez le portail. Les données persistent uniquement tant qu’elles se trouvent sur le graphique et sont ensuite ignorées. Toutes les fonctions sont disponibles pour les applications ASP.NET classiques, et les applications de base .NET n’incluent actuellement qu’un ensemble fixe de métriques temps réel et d’exemples d’échecs. Nous mettons à jour tous les Kits de développement logiciel (SDK) pour qu’ils prennent en charge les dernières fonctionnalités de streaming en direct. 

Nous collectons le flux temps réel de vos instances d’application avant l’échantillonnage, ou tous les TelemetryProcessors personnalisés sont appliqués. 

![Vidéo sur le flux de métriques temps réel](./media/app-insights-live-stream/youtube.png) [Vidéo sur le flux de métriques temps réel](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Accédez aux métriques et événements temps réel en cliquant sur l’option de gauche ou le bouton dans le panneau Vue d’ensemble :

![Dans le panneau Vue d'ensemble, cliquez sur Live Stream](./media/app-insights-live-stream/live-stream-2.png)

## <a name="custom-live-kpi"></a>Indicateur de performance clé (KPI) temps réel personnalisé
Vous pouvez surveiller un KPI personnalisé en temps réel en appliquant des filtres arbitraires sur les données de télémétrie Application Insights à partir du portail. Cliquez sur la commande de filtre qui s’affiche lorsque vous passez la souris sur un graphique. Le graphique suivant indique un KPI de nombre de demandes personnalisé avec des filtres sur les attributs d’URL et de durée. Vérifiez vos filtres grâce à la section Vue d’ensemble du flux qui affiche un flux en temps réel de télémétrie correspondant aux critères que vous avez spécifiés. 

![Indicateur de performance clé (KPI) de demande personnalisé](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Vous pouvez surveiller une valeur autre qu’un nombre. Les options varient selon le type de flux, qui peut être n’importe quelle télémétrie Application Insights : demandes, dépendances, exceptions, suivis, événements ou métriques. Il peut s’agir d’une [mesure personnalisée](app-insights-api-custom-events-metrics.md#properties) :

![Options de valeur](./media/app-insights-live-stream/live-stream-valueoptions.png)

En plus des données de télémétrie Application Insights, vous pouvez également surveiller n’importe quel compteur de performances Windows en le sélectionnant dans les options de flux et en fournissant le nom du compteur de performances.

Les métriques temps réel sont agrégées à deux endroits : localement sur chaque serveur, puis sur tous les serveurs. Vous pouvez modifier la valeur par défaut en sélectionnant d’autres options dans les listes déroulantes respectives.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Exemple de télémétrie : événements de diagnostic temps réel personnalisés
Par défaut, le flux d’événements temps réel présente des exemples de demandes ayant échoué et d’appels de dépendance, d’exceptions, d’événements et de suivis. Cliquez sur l’icône de filtre pour afficher les critères appliqués. 

![Flux temps réel par défaut](./media/app-insights-live-stream/live-stream-eventsdefault.png)

Comme avec les métriques, vous pouvez spécifier un critère arbitraire sur les types de données de télémétrie Application Insights. Dans cet exemple, nous sélectionnons des échecs de demande, des suivis et des événements spécifiques. Nous sélectionnons également toutes les exceptions et tous les échecs de dépendance.

![Flux temps réel personnalisé](./media/app-insights-live-stream/live-stream-events.png)

Remarque : pour les critères basés sur un message d’exception, utilisez le message d’exception le plus à l’extérieur. Dans l’exemple précédent, pour éliminer l’exception sans gravité avec le message d’exception interne (après le délimiteur « <-- ») « Client déconnecté ». utilisez un critère de message ne contenant pas « Erreur de lecture du contenu de la demande ».

Consultez les informations détaillées d’un élément dans le flux temps réel en cliquant dessus. Vous pouvez interrompre le flux en cliquant sur **Suspendre**, en faisant simplement défiler ou en cliquant sur un élément. Le flux temps réel reprend lorsque vous refaites défiler vers le haut, ou en cliquant sur le compteur d’éléments collectés alors qu’il était suspendu.

![Échecs dynamiques échantillonnés](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrer par instance de serveur

Si vous voulez surveiller une instance de rôle serveur spécifique, vous pouvez appliquer un filtre par serveur.

![Échecs dynamiques échantillonnés](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Configuration requise du Kit de développement logiciel (SDK)
Le flux de métriques temps réel personnalisé est disponible avec la version 2.4.0-beta2 ou plus récente du [Kit de développement logiciel (SDK) Application Insights pour le web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). N’oubliez pas de sélectionner l’option « Inclure la version préliminaire » dans le gestionnaire de package NuGet.

## <a name="authenticated-channel"></a>Canal authentifié
Les critères de filtres personnalisés que vous spécifiez sont renvoyés au composant de métriques temps réel dans le Kit de développement logiciel (SDK) Application Insights. Les filtres peuvent potentiellement contenir des informations sensibles telles que des ID clients. Vous pouvez sécuriser le canal avec une clé API secrète en plus de la clé d’instrumentation.
### <a name="create-an-api-key"></a>Création d’une clé API

![Création d’une clé API](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Ajout d’une clé API à la configuration
Dans le fichier applicationinsights.config, ajoutez AuthenticationApiKey à QuickPulseTelemetryModule :
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add> 

```
Ou dans le code, définissez-la sur QuickPulseTelemetryModule :

``` C#

    module.AuthenticationApiKey = "YOUR-API-KEY-HERE";

```

Toutefois, si vous connaissez et faites confiance à tous les serveurs connectés, vous pouvez essayer les filtres personnalisés sans le canal authentifié. Cette option est disponible pour six mois. Ce remplacement est nécessaire à chaque nouvelle session, ou lorsqu’un nouveau serveur est en ligne.

![Options d’authentification de métriques temps réel](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>Nous vous recommandons vivement de configurer le canal authentifié avant d’entrer des informations potentiellement sensibles comme un ID client dans les critères de filtre.
>

## <a name="troubleshooting"></a>Résolution des problèmes

Pas de données ? Si votre application se trouve dans un réseau protégé : le Flux de métriques temps réel utilise des adresses IP différentes d’autres données de télémétrie Application Insights. Assurez-vous que [ces adresses IP](app-insights-ip-addresses.md) sont ouvertes dans votre pare-feu.



## <a name="next-steps"></a>Étapes suivantes
* [Surveillance de l’utilisation avec Application Insights](app-insights-web-track-usage.md)
* [Utilisation de Diagnostic Search](app-insights-diagnostic-search.md)


