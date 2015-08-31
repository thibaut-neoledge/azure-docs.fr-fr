<properties 
	pageTitle="Gestion de la tarification et du quota pour Application Insights"
	description="Choisissez le plan de tarification dont vous avez besoin"
	services="application-insights"
	documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags 
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="awills"/>

# Gestion de la tarification et du quota pour Application Insights

*Application Insights est à l'état de version préliminaire.*

La [tarification][pricing] pour [Visual Studio Application Insights][start] est basée sur le volume de données par application. Un niveau Gratuit conséquent vous permet d’utiliser la plupart des fonctionnalités avec certaines restrictions.

Chaque ressource d’Application Insights est facturée comme un service distinct et s’ajoute à votre facture d’abonnement à Azure.

[Consultez le mécanisme de tarification][pricing].

## Examen du quota et du plan de tarification pour votre ressource Application Insights

Vous pouvez ouvrir le panneau relatif au quota et à la tarification depuis les paramètres de la ressource de votre application.

![Cliquez sur paramètres, Quota + tarification.](./media/app-insights-pricing/01-pricing.png)

Le mécanisme de tarification que vous choisissez a une incidence sur les éléments suivants :

* [Quota mensuel](#monthly-quota) : volume de données de télémétrie que vous pouvez analyser chaque mois.
* [Débit de données](#data-rate) : vitesse de traitement maximale des données de votre application.
* [Rétention](#data-retention) : durée pendant laquelle les données sont conservées dans le portail Application Insights à votre intention.
* [Exportation continue](#continuous-export) : possibilité ou non d’exporter des données vers d’autres outils et services.

Ces limites sont définies séparément pour chacune des ressources Application Insights.

### Version d’évaluation Premium gratuite

Lorsque vous créez une nouvelle ressource Application Insights, celle-ci démarre en mode gratuit.

Vous pouvez à tout moment passer à la version d’évaluation Premium gratuite de 30 jours. Celle-ci vous offre les avantages du niveau Premium. Après 30 jours, vous revenez automatiquement au niveau dans lequel vous vous trouviez précédemment, à moins d’avoir choisi explicitement un autre niveau. Vous sélectionnez le niveau souhaité à tout moment pendant la période d’évaluation, tout en bénéficiant toujours de l’évaluation gratuite jusqu’au terme de la période de 30 jours.


## Quota mensuel

* Chaque mois calendaire, votre application peut envoyer une quantité spécifique de données de télémétrie à Application Insights. Consultez le [mécanisme de tarification][pricing] pour les nombres réels. 
* Le quota varie selon le niveau de tarification que vous avez choisi.
* Le quota est calculé à partir de minuit (UTC), le premier jour de chaque mois.
* Le graphique de points de données indique la quantité de quota utilisée ce mois-ci.
* Le quota est mesuré en *points de données.* Un point de données est un appel à une des méthodes de suivi, qu’elle soit appelée explicitement dans votre code ou par l’un des modules standard de télémétrie. Les points de données incluent :
 * Chaque ligne affichée dans la [recherche de diagnostic](app-insights-diagnostic-search.md). 
 * Chaque mesure brute d’une [métrique](app-insights-metrics-explorer.md), comme un compteur de performances. (Les points que vous voyez sur les graphiques sont généralement des agrégats de plusieurs points de données brutes).
 * Chaque point sur les graphiques de [test web (disponibilité)](app-insights-monitor-web-app-availability.md). 
* Les *données de session* ne sont pas comptabilisées dans le quota. Cela inclut le nombre d’utilisateurs, de sessions et les données relatives à l’environnement et aux appareils.


### Dépassement

Si votre application dépasse le quota d’envoi mensuel, vous pouvez :

* Payer les données supplémentaires. Consultez le [mécanisme de tarification][pricing] pour obtenir des informations. Vous pouvez choisir cette option à l’avance. Cette option n’est pas disponible dans le niveau de tarification gratuit.
* Mettre à jour votre niveau de tarification.
* Ne rien faire. Les données de session continueront à être enregistrées, mais les autres données n’apparaîtront pas dans Recherche de diagnostic ou Metrics explorer.


### Quelle quantité de données suis-je en train d’envoyer ?

Le graphique au bas du panneau de tarification illustre le volume des points de données de votre application, regroupés par type. (Vous pouvez également créer ce graphique dans Metrics Explorer).

![Au bas du panneau de tarification](./media/app-insights-pricing/03-allocation.png)

Cliquez sur le graphique pour plus d’informations ou sélectionnez une partie de ce dernier et cliquez sur (+) pour obtenir des informations sur une période.


## Débit de données

Outre le quota mensuel, il existe des seuils de limitation en matière de débit de données. Pour le [niveau de tarification][pricing] Gratuit, la limite correspond à un débit moyen de 200 points de données par seconde sur une période de 5 minutes ; pour les niveaux de tarification payants, cette limite équivaut à un débit moyen de 500 points de données par seconde sur une période de 1 minute.

Il existe trois compartiments qui sont comptabilisés séparément :

* [appels TrackTrace](app-insights-api-custom-events-metrics.md#track-trace) et [journaux capturés](app-insights-asp-net-trace-logs.md) ;
* [exceptions](app-insights-api-custom-events-metrics.md#track-exception), limitées à 50 points/s ;
* toutes les autres données de télémétrie (pages consultées, sessions, demandes, dépendances, métriques, événements personnalisés et résultats des tests web).

Si le volume de données envoyées par votre application dépasse cette limite, certaines de ces données sont supprimées. Si tel est le cas, vous en êtes informé par un avertissement.

### Conseils destinés à réduire votre débit de données

Si vous rencontrez les seuils de limitation, voici quelques opérations à effectuer :

* Désactivez les modules de collecte dont vous n’avez pas besoin en [modifiant ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Par exemple, vous pouvez décider que les compteurs de performances ou les données de dépendance ne sont pas essentiels.
* Procédez à la pré-agrégation des métriques. Si vous avez placé des appels de TrackMetric dans votre application, vous pouvez réduire le trafic en utilisant la surcharge qui accepte votre calcul de la moyenne et de l’écart type d’un lot de mesures. Une autre possibilité consiste à utiliser un [package de pré-agrégation](https://www.myget.org/gallery/applicationinsights-sdk-labs). 


### Limites de nom

1.	Un maximum de 200 noms de mesure uniques et de 200 noms de propriété unique pour votre application. Les mesures comprennent l’envoi de données via TrackMetric ainsi que des mesures sur d’autres types de données tels que des événements. Les [noms de mesure et de propriété][api] sont globaux pour chaque clé d’instrumentation et ne s’étendent pas au type de données.
2.	Les [propriétés][apiproperties] peuvent être utilisées pour le filtrage et le regroupement uniquement lorsqu’il y a moins de 100 valeurs uniques pour chaque propriété. Lorsque les valeurs uniques dépassent 100, la propriété peut toujours être utilisée pour effectuer une recherche et un filtrage, mais elle ne peut plus être utilisée pour des filtres.
3.	Les propriétés standard telles que le nom de la requête et l’URL de la page sont limitées à 1 000 valeurs uniques par semaine. Au-delà de 1 000 valeurs uniques, les valeurs supplémentaires sont marquées comme « Autres valeurs ». La valeur d'origine peut toujours être utilisée pour une recherche de texte intégrale et pour le filtrage.

## Conservation des données

Votre niveau de tarification détermine la durée de conservation des données dans le portail, et donc les intervalles de temps les plus éloignés dont vous pouvez consulter les données.


* Les points de données bruts (autrement dit, les instances que vous pouvez inspecter dans Recherche de diagnostic) sont conservés entre 7 et 30 jours.
* Les données agrégées (autrement dit, les nombres, moyennes et autres données statistiques que vous voyez dans Metrics Explorer) sont conservées pour une minute pendant 30 jours et pour une heure ou un jour (selon le type) pendant 13 mois.




## Consultation de la facture de votre abonnement à Azure

Les frais liés à Application Insights sont ajoutés à votre facture Azure. Les informations relatives à votre facture Azure s’affichent dans la section Facturation du portail Azure ou sur le [portail de facturation Azure](https://account.windowsazure.com/Subscriptions).

![Dans le menu latéral, choisissez Facturation.](./media/app-insights-pricing/02-billing.png)

## Synthèse des limites

[AZURE.INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-get-started.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=August15_HO8-->