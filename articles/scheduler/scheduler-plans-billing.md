<properties
 pageTitle="Plans et facturation dans Azure Scheduler"
 description="Plans et facturation dans Azure Scheduler"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="krisragh"/>

# Plans et facturation dans Azure Scheduler

## Plans de collections de travaux

Dans Azure Scheduler, les collections de travaux constituent l'entité facturable. Les collections de travaux contiennent plusieurs travaux et se présentent en trois modes (Gratuit, Standard et Premium) décrits ci-dessous.

|**Plan de collection de travaux**|**Nombre maximal de travaux par Collection de travaux**|**Périodicité maximale**|**Collections de travail max. par abonnement**|**Limites**|
|:---|:---|:---|:---|:---|
|**Gratuit**|5 travaux par collection|Une fois par heure. Ne peut pas exécuter des travaux plus souvent qu'une fois par heure|1 collection de travaux gratuite maximum est autorisée par abonnement|Impossible d'utiliser un [objet d'autorisation sortante HTTP](scheduler-outbound-authentication.md)
|**Standard**|50 travaux par collection|Une fois par minute. Ne peut pas exécuter des travaux plus souvent qu'une fois par minute|100 collections de travaux standard maximum sont autorisées par abonnement|Accès à l'ensemble complet des fonctionnalités de Scheduler|
|**P10 Premium**|50 travaux par collection|Une fois par minute. Ne peut pas exécuter des travaux plus souvent qu'une fois par minute|10 000 collections de travaux P10 Premium maximum sont autorisées par abonnement. Pour augmenter cette limite, <a href="mailto:wapteams@microsoft.com">contactez-nous</a>.|Accès à l'ensemble complet des fonctionnalités de Scheduler|
|**P20 Premium**|1000 travaux par collection|Une fois par minute. Ne peut pas exécuter des travaux plus souvent qu'une fois par minute|10 000 collections de travaux P20 Premium maximum sont autorisées par abonnement. Pour augmenter cette limite, <a href="mailto:wapteams@microsoft.com">contactez-nous</a>.|Accès à l'ensemble complet des fonctionnalités de Scheduler|

## Mises à niveau et versions antérieures des Plans de collections de travaux

Vous pouvez mettre à niveau ou rétrograder un plan de collection de travaux à tout moment en choisissant entre les plans Gratuit, Standard et Premium. Toutefois, lorsque vous passez à une collection de travaux gratuite, la rétrogradation peut échouer pour l'une des raisons suivantes :

- Il existe déjà une collection de travaux gratuite dans l'abonnement
- Un travail de la collection de travaux a une périodicité supérieure à celle autorisée pour les travaux dans les collections de travaux gratuites. La périodicité maximale autorisée dans une collection de travaux gratuite est une fois par heure.
- La collection de travaux contient plus de 5 travaux.
- Un travail de la collection de travaux a une action HTTP ou HTTPS qui utilise un [objet d'autorisation sortante HTTP](scheduler-outbound-authentication.md)

## Facturation et plans Azure

Les abonnements ne sont pas facturés pour les collections de travaux gratuites. Si vous avez plus de 100 collections de travaux standard (10 unités de facturation standard), il est plus intéressant de regrouper toutes les collections de travaux dans le plan Premium.

Si vous disposez d’une collection de travaux Standard et d’une collection de travaux Premium, une unité de facturation standard _et_ une unité de facturation premium vous sont facturées. Le service de Scheduler facture en fonction du nombre de collections de travaux actives qui sont définies sur Standard ou Premium. Ceci est expliqué dans les deux sections suivantes.

## Unités facturables standard

Une unité facturable standard peut inclure jusqu'à 10 collections de travaux standard. Dans la mesure où une collection de travaux standard peut contenir jusqu'à 50 travaux par collection de travaux, une unité de facturation standard permet à un abonnement de contenir jusqu'à 500 travaux (jusqu'à environ 22 millions d'exécutions de travaux par mois).

Si vous avez entre 1 et 10 collections de travaux standard, vous serez facturé pour 1 unité de facturation standard. Si vous avez entre 11 et 20 collections de travaux standard, vous serez facturé pour 2 unités de facturation standard. Si vous avez entre 21 et 30 collections de travaux standard, vous serez facturé pour 3 unités de facturation standard, et ainsi de suite.

## Unités facturables P10 Premium

Une unité facturable P10 Premium peut inclure jusqu'à 10 000 collections de travaux P10 Premium. Dans la mesure où une collection de travaux P10 Premium peut contenir jusqu'à 50 travaux par collection de travaux, une unité de facturation premium permet à un abonnement de contenir jusqu'à 500 000 travaux (jusqu'à environ 22 milliards d'exécutions de travaux par mois).

Si vous avez entre 1 et 10 000 collections de travaux premium, vous serez facturé pour 1 unité de facturation P10 Premium. Si vous avez entre 10 001 et 20 000 collections de travaux premium, vous serez facturé pour 2 unité de facturation P10 Premium, et ainsi de suite.

Ainsi, les collections de travaux P10 Premium ont les mêmes fonctionnalités que les collections de travaux standard, mais elles fournissent une rupture des prix au cas où votre application nécessite un grand nombre de collections de travaux.

## Unités facturables P20 Premium

Une unité facturable P20 Premium peut inclure jusqu'à 5 000 collections de travaux P20 Premium. Dans la mesure où une collection de travaux P20 Premium peut contenir jusqu'à 1 000 travaux par collection de travaux, une unité de facturation Premium permet à un abonnement de contenir jusqu'à 5 000 000 travaux (jusqu'à environ 220 milliards d'exécutions de travaux par mois).

Les collections de travaux P20 Premium proposent les mêmes fonctionnalités que les collections de travaux P10 Premium, mais elles prennent également en charge un plus grand nombre de travaux par collection ainsi qu’un plus grand nombre total de travaux, ce qui vous offre plus d’évolutivité.

## Facturation et état Actif

Les collections de travaux sont toujours actives, sauf si votre abonnement entier est temporairement à l’état désactivé en raison de problèmes de facturation. La seule façon de s'assurer qu'une collection de travaux n'est pas facturée est de la définir dans le plan _Gratuit_ ou de la supprimer.

Bien que vous puissiez désactiver tous les travaux d’une collection de travaux en une seule opération, cela ne modifie pas l’état de facturation de la collection de travaux : celle-ci sera _tout de même_ facturée. De même, les collections de travaux vides sont considérées comme actives et seront facturées.

## Tarification

Pour plus d’informations sur la tarification, voir l’article [Tarification d’Azure Scheduler](https://azure.microsoft.com/pricing/details/scheduler/).

## Voir aussi


 [Présentation d'Azure Scheduler](scheduler-intro.md)

 [Concepts, terminologie et hiérarchie d’entités d’Azure Scheduler](scheduler-concepts-terms.md)

 [Prise en main de Scheduler dans le portail Azure](scheduler-get-started-portal.md)

 [Informations de référence sur l’API REST d’Azure Scheluler](https://msdn.microsoft.com/library/mt629143)

 [Informations de référence sur les applets de commande PowerShell d’Azure Scheluler](scheduler-powershell-reference.md)

 [Haute disponibilité et fiabilité d’Azure Scheluler](scheduler-high-availability-reliability.md)

 [Limites, valeurs par défaut et codes d’erreur d’Azure Scheluler](scheduler-limits-defaults-errors.md)

 [Authentification sortante d’Azure Scheluler](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0824_2016-->