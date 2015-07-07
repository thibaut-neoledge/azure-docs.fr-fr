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
	ms.date="06/09/2015" 
	ms.author="awills"/>

# Gestion de la tarification et du quota pour Application Insights

*Application Insights est à l'état de version préliminaire.*

La [tarification][pricing] pour [Visual Studio Application Insights][start] est basée sur le volume de données par application. Un niveau Gratuit conséquent vous permet d’utiliser la plupart des fonctionnalités avec certaines restrictions.

Chaque ressource d’Application Insights est facturée comme un service distinct et s’ajoute à votre facture d’abonnement à Azure.

[Consultez le mécanisme de tarification][pricing].

## Examen du quota et du plan de tarification pour votre ressource Application Insights

Vous pouvez ouvrir le panneau relatif au quota et à la tarification depuis les paramètres de la ressource de votre application.

![Cliquez sur paramètres, Quota + tarification.](./media/app-insights-pricing/01-pricing.png)

## Comment fonctionne le quota ?

* Chaque mois calendaire, votre application peut envoyer une quantité spécifique de données de télémétrie à Application Insights. Consultez le [mécanisme de tarification][pricing] pour les nombres réels. 
* Le quota varie selon le niveau de tarification que vous avez choisi.
* Le quota est calculé à partir de minuit (UTC), le premier jour de chaque mois.
* Le graphique de points de données indique la quantité de quota utilisée ce mois-ci.
* Le quota est mesuré en *points de données.* Un point de données est un appel à une des méthodes de suivi, qu’elle soit appelée explicitement dans votre code ou par l’un des modules standard de télémétrie. Chaque ligne affichée dans la recherche diagnostic correspond à un point de données. Chaque mesure d’une métrique, comme un compteur de performance, correspond à un point de données. 
* Les *données de session* ne sont pas comptabilisées dans le quota. Cela inclut le nombre d’utilisateurs, de sessions et les données relatives à l’environnement et aux appareils.


## Dépassement

Si votre application dépasse le quota d’envoi mensuel, vous pouvez :

* Payer les données supplémentaires. Consultez le [mécanisme de tarification][pricing] pour obtenir des informations. Vous pouvez choisir cette option à l’avance. Cette option n’est pas disponible dans le niveau de tarification gratuit.
* Mettre à jour votre niveau de tarification.
* Ne rien faire. Les données de session continueront à être enregistrées, mais les autres données n’apparaîtront pas dans Recherche de diagnostic ou Metrics explorer.

## Version d’évaluation Premium gratuite

Lorsque vous créez une nouvelle ressource Application Insights, celle-ci démarre en mode gratuit.

Vous pouvez à tout moment passer à la version d’évaluation Premium gratuite de 30 jours. Celle-ci vous offre les avantages du niveau Premium. Après 30 jours, vous revenez automatiquement au niveau dans lequel vous vous trouviez précédemment, à moins d’avoir choisi explicitement un autre niveau. Vous sélectionnez le niveau souhaité à tout moment pendant la période d’évaluation, tout en bénéficiant toujours de l’évaluation gratuite jusqu’au terme de la période de 30 jours.


## Comment mon quota a été utilisé ?

Le graphique en bas du panneau de tarification illustre l’utilisation des points de données de votre application, regroupées par type.

![Au bas du panneau de tarification](./media/app-insights-pricing/03-allocation.png)

Cliquez sur le graphique pour plus d’informations ou sélectionnez une partie de ce dernier pour obtenir des informations sur une période.

## Consultation de la facture de votre abonnement à Azure

Les frais liés à Application Insights sont ajoutés à votre facture Azure. Les informations relatives à votre facture Azure s’affichent dans la section Facturation du portail Azure ou sur le [portail de facturation Azure](https://account.windowsazure.com/Subscriptions).

![Dans le menu latéral, choisissez Facturation.](./media/app-insights-pricing/02-billing.png)




<!--Link references-->


[start]: app-insights-get-started.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=62-->