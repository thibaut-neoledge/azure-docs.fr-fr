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
	ms.date="03/15/2016" 
	ms.author="awills"/>

# Gestion de la tarification et du quota pour Application Insights

*Application Insights est à l'état de version préliminaire.*

La [tarification][pricing] pour [Visual Studio Application Insights][start] est basée sur le volume de données par application. Un niveau Gratuit conséquent vous permet d’utiliser la plupart des fonctionnalités avec certaines restrictions.

Chaque ressource d’Application Insights est facturée comme un service distinct et s’ajoute à votre facture d’abonnement à Azure.

[Consultez le mécanisme de tarification][pricing].

## Examen du quota et du plan de tarification pour votre ressource Application Insights

Vous pouvez ouvrir le panneau relatif au quota et à la tarification depuis les paramètres de la ressource de votre application.

![Cliquez sur paramètres, Quota + tarification.](./media/app-insights-pricing/01-pricing.png)

Le mécanisme de tarification que vous choisissez a une incidence sur les éléments suivants :

* [Quota mensuel](#monthly-quota) : volume de données de télémétrie que vous pouvez analyser chaque mois.
* [Débit de données](#data-rate) : vitesse de traitement maximale des données de votre application.
* [Rétention](#data-retention) : durée pendant laquelle les données sont conservées dans le portail Application Insights à votre intention.
* [Exportation continue](#continuous-export) : possibilité ou non d’exporter des données vers d’autres outils et services.

Ces limites sont définies séparément pour chacune des ressources Application Insights.

### Version d’évaluation Premium gratuite

Lorsque vous créez une nouvelle ressource Application Insights, celle-ci démarre en mode gratuit.

Vous pouvez à tout moment passer à la version d’évaluation Premium gratuite de 30 jours. Celle-ci vous offre les avantages du niveau Premium. Après 30 jours, vous revenez automatiquement au niveau dans lequel vous vous trouviez précédemment, à moins d’avoir choisi explicitement un autre niveau. Vous sélectionnez le niveau souhaité à tout moment pendant la période d’évaluation, tout en bénéficiant toujours de l’évaluation gratuite jusqu’au terme de la période de 30 jours.


## Quota mensuel

* Chaque mois calendaire, votre application peut envoyer une quantité spécifique de données de télémétrie à Application Insights. Actuellement, le quota pour le niveau tarifaire gratuit est de 5 millions de points de données par mois, et beaucoup plus pour les autres schémas. Vous pouvez en acheter plus si vous atteignez le quota. Consultez le [mécanisme de tarification][pricing] pour les nombres réels. 
* Le quota varie selon le niveau de tarification que vous avez choisi.
* Le quota est calculé à partir de minuit (UTC), le premier jour de chaque mois.
* Le graphique de points de données indique la quantité de quota utilisée ce mois-ci.
* Le quota est mesuré en *points de données.* Un point de données est un appel à une des méthodes de suivi, qu’elle soit appelée explicitement dans votre code ou par l’un des modules standard de télémétrie. 
* Les points de données sont générées par les éléments suivants :
 * [Les modules du Kit de développement logiciel (SDK)](app-insights-configuration-with-applicationinsights-config.md) qui collectent automatiquement les données, par exemple pour signaler une requête ou une panne, ou pour mesurer les performances.
 * [Les appels d’API](app-insights-api-custom-events-metrics.md) `Track...` que vous avez écrits, tels que `TrackEvent` ou `trackPageView`.
 * [Les tests web de disponibilité](app-insights-monitor-web-app-availability.md) que vous avez définis.
* Pendant le débogage, vous pouvez voir les points de données envoyés à partir de votre application dans la fenêtre Sortie de Visual Studio. Les événements clients peuvent être consultés en ouvrant l’onglet Réseau dans le volet de débogage de votre navigateur (généralement, F12).
* Les *données de session* ne sont pas comptabilisées dans le quota. Cela inclut le nombre d’utilisateurs, de sessions et les données relatives à l’environnement et aux appareils.
* Pour connaître le nombre de points de données par inspection, vous pouvez consulter divers emplacements :
 * Chaque élément que vous voyez dans la [recherche de diagnostic](app-insights-diagnostic-search.md), ce qui inclut les requêtes HTTP, les exceptions, les suivis des journaux, les affichages de pages, les événements de dépendance et les événements personnalisés.
 * Chaque mesure brute d’une [métrique](app-insights-metrics-explorer.md), comme un compteur de performances. (Les points que vous voyez sur les graphiques sont généralement des agrégats de plusieurs points de données brutes).
 * Chaque point sur un graphique de disponibilité web est également un agrégat de plusieurs points de données.
* Vous pouvez également inspecter des points de données individuels à la source pendant le débogage :
 * Si vous exécutez votre application en mode débogage dans Visual Studio, les points de données sont enregistrés dans la fenêtre Sortie. 
 * Pour afficher les points de données clients, ouvrez le volet de débogage de votre navigateur (généralement, F12), puis ouvrez l’onglet Réseau.
* Le débit de données est (par défaut) réduit par [échantillonnage adaptatif](app-insights-sampling). Autrement dit, lorsque l’utilisation de votre application augmente, le taux de télémétrie n’augmente pas autant que vous pouviez le penser.

### Dépassement

Si votre application dépasse le quota d’envoi mensuel, vous pouvez :

* Payer les données supplémentaires. Consultez le [mécanisme de tarification][pricing] pour obtenir des informations. Vous pouvez choisir cette option à l’avance. Cette option n’est pas disponible dans le niveau de tarification gratuit.
* Mettre à jour votre niveau de tarification.
* Ne rien faire. Les données de session continueront à être enregistrées, mais les autres données n’apparaîtront pas dans Recherche de diagnostic ou Metrics explorer.


### Quelle quantité de données suis-je en train d’envoyer ?

Le graphique au bas du panneau de tarification illustre le volume des points de données de votre application, regroupés par type. (Vous pouvez également créer ce graphique dans Metrics Explorer).

![Au bas du panneau de tarification](./media/app-insights-pricing/03-allocation.png)

Cliquez sur le graphique pour plus d’informations ou sélectionnez une partie de ce dernier et cliquez sur (+) pour obtenir des informations sur une période.

Le graphique affiche le volume de données qui arrive au niveau du service Application Insights, après l’[échantillonnage](app-insights-sampling).


## Débit de données

Outre le quota mensuel, il existe des seuils de limitation en matière de débit de données. Pour le [niveau tarifaire][pricing] gratuit, la limite correspond à un débit moyen de 200 points de données par seconde sur une période de 5 minutes ; pour les niveaux tarifaires payants, cette limite équivaut à un débit moyen de 500 points de données par seconde sur une période de 1 minute.

Il existe trois compartiments qui sont comptabilisés séparément :

* [appels TrackTrace](app-insights-api-custom-events-metrics.md#track-trace) et [journaux capturés](app-insights-asp-net-trace-logs.md) ;
* [exceptions](app-insights-api-custom-events-metrics.md#track-exception), limitées à 50 points/s ;
* toutes les autres données de télémétrie (pages consultées, sessions, demandes, dépendances, métriques, événements personnalisés et résultats des tests web).





*Que se passe-t-il si mon application dépasse le taux par seconde ?*

* Le volume de données que votre application envoie est évalué à chaque minute. S’il dépasse le taux par seconde moyen de plus d’une minute, le serveur refuse des demandes. Certaines versions du Kit de développement logiciel (SDK) essaient de renvoyer, en diffusant un pic pendant plusieurs minutes ; d’autres, comme le SDK JavaScript, abandonnent simplement les données refusées.

En cas de limitation, vous en êtes informé par un avertissement.

*Comment savoir combien de points de données envoie mon application ?*

* Ouvrez Paramètres/Quota et tarification pour découvrir le graphique du volume de données.
* Ou dans Metrics Explorer, ajoutez un nouveau graphique et sélectionnez la mesure **Volume du point de données**. Basculez sur le regroupement et regroupez par **Type de données**.


### Conseils destinés à réduire votre débit de données

Si vous rencontrez les seuils de limitation, voici quelques opérations à effectuer :

* Utilisez l’[échantillonnage](app-insights-sampling.md). Cette technologie réduit le débit de données sans fausser vos mesures et sans impacter la capacité à naviguer entre des éléments associés dans la recherche.
* [Limitez le nombre d'appels Ajax qui peuvent être signalés](app-insights-javascript.md#detailed-configuration) dans chaque affichage de page, ou désactivez les rapports Ajax.
* Désactivez les modules de collecte dont vous n'avez pas besoin en [modifiant ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Par exemple, vous pouvez décider que les compteurs de performances ou les données de dépendance ne sont pas essentiels.
* Procédez à la pré-agrégation des métriques. Si vous avez placé des appels de TrackMetric dans votre application, vous pouvez réduire le trafic en utilisant la surcharge qui accepte votre calcul de la moyenne et de l’écart type d’un lot de mesures. Une autre possibilité consiste à utiliser un [package de pré-agrégation](https://www.myget.org/gallery/applicationinsights-sdk-labs). 


### Limites de nom

1.	Un maximum de 200 noms de mesure uniques et de 200 noms de propriété unique pour votre application. Les métriques comprennent les données envoyées via TrackMetric, ainsi que des mesures sur d’autres types de données tels que des événements. Les [noms de mesure et de propriété][api] sont globaux pour chaque clé d’instrumentation et ne s’étendent pas au type de données.
2.	Les [propriétés][apiproperties] peuvent être utilisées pour le filtrage et le regroupement uniquement lorsqu’il y a moins de 100 valeurs uniques pour chaque propriété. Lorsque les valeurs uniques dépassent 100, la propriété peut toujours être utilisée pour effectuer une recherche et un filtrage, mais elle ne peut plus être utilisée pour des filtres.
3.	Les propriétés standard telles que le nom de la requête et l'URL de la page sont limitées à 1 000 valeurs uniques par semaine. Au-delà de 1 000 valeurs uniques, les valeurs supplémentaires sont marquées comme « Autres valeurs ». La valeur d'origine peut toujours être utilisée pour une recherche de texte intégrale et pour le filtrage.

## Conservation des données

Votre niveau de tarification détermine la durée de conservation des données dans le portail, et donc les intervalles de temps les plus éloignés dont vous pouvez consulter les données.


* Les points de données bruts (autrement dit, les instances que vous pouvez inspecter dans Recherche de diagnostic) : 7 jours.
* Les données agrégées (autrement dit, les nombres, moyennes et autres données statistiques que vous voyez dans Metrics Explorer) sont conservées pour une minute pendant 30 jours et pour une heure ou un jour (selon le type) pendant 90 mois.


## Échantillonnage

L’[échantillonnage](app-insights-sampling.md) est une méthode vous permettant de réduire les données de télémétrie conservées par votre application, tout en conservant la capacité de trouver des événements connexes lors des recherches de diagnostic, ainsi que le décompte des événements corrects. L’échantillonnage vous permet de respecter votre quota mensuel.

Il existe plusieurs formes d’échantillonnage. Nous vous recommandons d’utiliser l’[échantillonnage adaptatif](app-insights-sampling.md), qui s’ajuste automatiquement au volume de télémétrie que votre application envoie. Il fonctionne dans le Kit de développement logiciel (SDL) de votre application web, afin de réduire le trafic de télémétrie sur le réseau. Vous pouvez l’utiliser si l’infrastructure de votre application web est .NET : installez la dernière version (bêta) du Kit de développement logiciel (SDK).

Comme alternative, vous pouvez définir l’*échantillonnage d’ingestion* dans le panneau Quotas et tarification. Cette forme d’échantillonnage fonctionne au niveau où les données de télémétrie issues de votre application entrent dans le service Application Insights. Il n’affecte pas le volume de télémétrie envoyé depuis votre application, mais il réduit le volume conservé par le service.

![Dans le panneau Quotas et tarification, cliquez sur la vignette Exemples et sélectionnez une fraction d’échantillonnage.](./media/app-insights-sampling/04.png)

L’échantillonnage est un moyen efficace de réduire les coûts et de respecter votre quota mensuel. L’algorithme d’échantillonnage conserve les éléments associés à la télémétrie, afin que, lorsque vous utilisez la recherche par exemple, vous puissiez trouver la demande liée à une exception spécifique. L’algorithme conserve également le décompte correct. Cela vous permet de voir les valeurs correctes des taux de demandes, des taux d’exception et des autres compteurs dans Metric Explorer.


## Consultation de la facture de votre abonnement à Azure

Les frais liés à Application Insights sont ajoutés à votre facture Azure. Les informations relatives à votre facture Azure s’affichent dans la section Facturation du portail Azure ou sur le [portail de facturation Azure](https://account.windowsazure.com/Subscriptions).

![Dans le menu latéral, choisissez Facturation.](./media/app-insights-pricing/02-billing.png)

## Synthèse des limites

[AZURE.INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=AcomDC_0316_2016-->