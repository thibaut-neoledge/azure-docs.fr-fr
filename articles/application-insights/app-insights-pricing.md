---
title: "Gérer le volume de données et les fonctionnalités pour Application Insights | Microsoft Docs"
description: "Gérer les volumes de données de télémétrie et surveiller les coûts dans Application Insights."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 3fdb050d1d6746313b2dffb089d56b71908335d2
ms.openlocfilehash: 585e493b2642b3ba798977528f7564674a44d738


---
# <a name="manage-features-and-data-volume-in-application-insights"></a>Gérer le volume de données et les fonctionnalités dans Application Insights


La tarification pour [Azure Application Insights][start] est basée sur le volume de données par application. Une faible utilisation pendant le développement ou pour une petite application est susceptible d’être gratuite, compte tenu du quota mensuel gratuit de données de télémétrie.

Chaque ressource d’Application Insights est facturée comme un service distinct et s’ajoute à votre facture d’abonnement à Azure.

Il existe deux plans de tarification. Le plan par défaut est nommé De base. Vous pouvez opter pour le plan Entreprise, facturé à la journée, qui donne accès à certaines fonctionnalités supplémentaires telles que [l’exportation continue](app-insights-export-telemetry.md).

[Consultez le plan de tarification][pricing].

## <a name="review-pricing-plan-for-your-application-insights-resource"></a>Examen du plan de tarification pour votre ressource Application Insights
Ouvrez le panneau Fonctionnalités + Tarification dans la ressource Application Insights de votre application.

![Choisissez Tarification.](./media/app-insights-pricing/01-pricing.png)

1. Consultez votre volume de données pour le mois. Cela inclut toutes les données reçues et conservées (après tous les [échantillonnages](app-insights-sampling.md) provenant de votre serveur, de vos applications clientes et des tests de disponibilité).
2. Les [tests web multiétapes](app-insights-monitor-web-app-availability.md#multi-step-web-tests) font l’objet d’une facturation distincte. (Cela n’inclut pas les tests de disponibilité simples, qui sont compris dans les frais de volume de données.)
3. Activez les fonctionnalités supplémentaires offertes par le plan Entreprise. Ce plan ne prévoit pas de quota de données gratuites.
4. Cliquez pour accéder aux options de gestion des données et définir une limite quotidienne ou un échantillonnage d’ingestion.

Les frais liés à Application Insights sont ajoutés à votre facture Azure. Les informations relatives à votre facture Azure s’affichent dans la section Facturation du portail Azure ou sur le [portail de facturation Azure](https://account.windowsazure.com/Subscriptions). 

![Dans le menu latéral, choisissez Facturation.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Débit de données
Le volume d’envoi de données est limité de trois façons :

* Limite quotidienne. Elle est fixée par défaut à 100 Go par jour. Lorsque votre application atteint le plafond, nous envoyons un courrier électronique et ignorons les données jusqu’à la fin de la journée. Vous pouvez la modifier sur le panneau de gestion des volumes de données.
* [Échantillonnage](app-insights-sampling.md). Ce mécanisme peut réduire la quantité de données de télémétrie envoyées à partir de votre serveur et de vos applications clientes, avec une distorsion minimale des métriques.
* La limitation de bande passante restreint le débit de données par minute. Pour le plan de tarification De base, la limite correspond à un débit moyen de 200 points de données par seconde sur une période de cinq minutes ; pour le plan de tarification Entreprise, cette limite équivaut à un débit moyen de 500 points de données par seconde sur une période d’une minute. 

En ce qui concerne la limitation de bande passante, trois compartiments sont comptabilisés séparément :

* [appels TrackTrace](app-insights-api-custom-events-metrics.md#track-trace) et [journaux capturés](app-insights-asp-net-trace-logs.md) ;
* [exceptions](app-insights-api-custom-events-metrics.md#track-exception), limitées à 50 points/s ;
* toutes les autres données de télémétrie (pages consultées, sessions, demandes, dépendances, métriques, événements personnalisés et résultats des tests web).

*Que se passe-t-il si mon application dépasse le taux par seconde ?*

* Le volume de données que votre application envoie est évalué à chaque minute. S’il dépasse le taux par seconde moyen de plus d’une minute, le serveur refuse des demandes. Le Kit de développement logiciel (SDK) met les données en mémoire tampon et essaie ensuite de renvoyer, en diffusant un pic pendant plusieurs minutes. Si votre application envoie régulièrement des données au-delà du taux limite, certaines données sont supprimées. (les kits de développement logiciel (SDK) ASP.NET, Java et JavaScript essaient de renvoyer de cette manière ; les autres SDK suppriment parfois simplement les données limitées).

En cas de limitation, vous en êtes informé par un avertissement.

*Comment savoir combien de points de données envoie mon application ?*

* Ouvrez le panneau Tarification pour afficher le graphique Volume de données.
* Ou dans Metrics Explorer, ajoutez un nouveau graphique et sélectionnez la mesure **Volume du point de données** . Basculez sur le regroupement et regroupez par **Type de données**.

## <a name="to-reduce-your-data-rate"></a>Pour réduire le débit de données
Voici quelques opérations possibles pour réduire le volume de données :

* Réduire la limite de volume quotidienne. La limite par défaut est de 100 Go par jour.
* Utilisez l’ [échantillonnage](app-insights-sampling.md). Cette technologie réduit le débit de données sans fausser vos mesures et sans impacter la capacité à naviguer entre des éléments associés dans la recherche. Dans les applications serveurs, elle s’applique automatiquement.
* [Limitez le nombre d’appels Ajax qui peuvent être signalés](app-insights-javascript.md#detailed-configuration) dans chaque affichage de page, ou désactivez les rapports Ajax.
* Désactivez les modules de collecte dont vous n'avez pas besoin en [modifiant ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Par exemple, vous pouvez décider que les compteurs de performances ou les données de dépendance ne sont pas essentiels.
* Diviser vos données de télémétrie pour séparer les clés d’instrumentation. 
* Procédez à la pré-agrégation des métriques. Si vous avez placé des appels de TrackMetric dans votre application, vous pouvez réduire le trafic en utilisant la surcharge qui accepte votre calcul de la moyenne et de l’écart type d’un lot de mesures. Une autre possibilité consiste à utiliser un [package de pré-agrégation](https://www.myget.org/gallery/applicationinsights-sdk-labs). 

## <a name="sampling"></a>échantillonnage
[Sampling](app-insights-sampling.md) est une méthode vous permettant de réduire la fréquence d’envoi des données de télémétrie à votre application, tout en conservant la capacité à trouver des événements connexes lors des recherches de diagnostic, ainsi que le décompte des événements corrects. 

L’échantillonnage est un moyen efficace de réduire les coûts et de respecter votre quota mensuel. L’algorithme d’échantillonnage conserve les éléments associés à la télémétrie, afin que, lorsque vous utilisez la recherche par exemple, vous puissiez trouver la demande liée à une exception spécifique. L’algorithme conserve également le décompte correct. Cela vous permet de voir les valeurs correctes des taux de demandes, des taux d’exception et des autres compteurs dans Metric Explorer.

Il existe plusieurs formes d’échantillonnage.

* [L’échantillonnage adaptatif](app-insights-sampling.md) est la méthode par défaut pour le kit de développement logiciel (SDK) ASP.NET. Il s’ajuste automatiquement au volume de données de télémétrie envoyées par votre application. Il fonctionne automatiquement dans le Kit de développement logiciel (SDK) de votre application web, afin de réduire le trafic de télémétrie sur le réseau. 
* *échantillonnage d’ingestion* est une méthode alternative qui fonctionne au niveau où les données de télémétrie issues de votre application entrent dans le service Application Insights. Il n’affecte pas le volume de télémétrie envoyé depuis votre application, mais il réduit le volume conservé par le service. Vous pouvez l’utiliser pour réduire le quota utilisé par les données de télémétrie provenant des navigateurs et d’autres kits de développement logiciel (SDK).

Pour définir l’échantillonnage d’ingestion, définissez le contrôle dans le panneau Tarification :

![Dans le panneau Quotas et tarification, cliquez sur la vignette Exemples et sélectionnez une fraction d’échantillonnage.](./media/app-insights-pricing/04.png)

> [!WARNING]
> La valeur affichée sur la vignette Échantillons conservés indique uniquement la valeur que vous avez définie pour l’échantillonnage d’ingestion. Elle n’indique pas le taux d’échantillonnage en vigueur au niveau du kit de développement logiciel (SDK) dans votre application. 
> 
> Si les données de télémétrie entrantes ont déjà été échantillonnées dans le kit de développement logiciel (SDK), l’échantillonnage d’ingestion n’est pas appliqué.
> 
> 

Pour découvrir le taux d’échantillonnage réel, indépendamment de l’endroit où il a été appliqué, utilisez une [requête Analytics](app-insights-analytics.md) telle que celle-ci :

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

Pour chaque enregistrement conservé, `itemCount` indique le nombre d’enregistrements d’origine qu’il représente, soit 1 + le nombre d’enregistrements précédents ignorés. 


## <a name="limits-summary"></a>Synthèse des limites
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Échantillonnage](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/




<!--HONumber=Nov16_HO3-->


