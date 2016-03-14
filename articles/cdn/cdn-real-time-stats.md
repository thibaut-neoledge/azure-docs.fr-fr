<properties
	pageTitle="CDN - Statistiques en temps réel"
	description="Statistiques en temps réel dans Microsoft Azure CDN. Les statistiques en temps réel fournissent des données en temps réel sur les performances de notre CDN lors de la diffusion de contenu à vos clients."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>

# Statistiques en temps réel dans Microsoft Azure CDN

## Vue d'ensemble

Ce document explique les statistiques en temps réel dans Microsoft Azure CDN. Cette fonctionnalité fournit des données en temps réel sur les performances de notre CDN lors de la diffusion de contenu à vos clients.

> [AZURE.NOTE] Les statistiques en temps réel sont une fonctionnalité du niveau CDN Premium. Pour comparer les fonctionnalités CDN Standard et Premium, consultez [Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](cdn-overview.md).

Les graphiques suivants sont disponibles quand vous consultez les statistiques en temps réel pour les plateformes HTTP :

* [Bande passante](#bandwidth)
* [Codes d’état](#status-codes)
* [États du cache](#cache-statuses)
* [Connexions](#connections)

> [AZURE.NOTE] Chacun des graphiques ci-dessus affiche les statistiques en temps réel pendant une période donnée. Une fenêtre glissante de données s’affiche une fois le délai spécifié écoulé. Cela signifie que les anciennes données sont supprimées du graphique pour laisser la place aux nouvelles données. La durée de cette fenêtre glissante peut être définie par l’option d’intervalle de temps des graphiques.

## Accès à des statistiques en temps réel

1. Dans le panneau de profil CDN, cliquez sur le bouton **Gérer**.

	![Bouton Gérer du panneau de profil CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)

	Le portail de gestion CDN s’ouvre.

2. Pointez sur l’onglet **Analyse**, puis sur le menu volant **Statistiques en temps réel**. Cliquez sur **HTTP Large Platform**.

	Les options de rapport sont affichées.

## Bande passante

Le graphique Bande passante montre la quantité de bande passante utilisée pour la plateforme actuelle sur une période spécifiée. La partie ombrée du graphique indique l’utilisation de la bande passante. La quantité exacte de bande passante en cours d’utilisation s’affiche directement sous le graphique linéaire.

> [AZURE.NOTE] Les unités utilisées pour indiquer l’utilisation de la bande passante sont les suivantes : bits par seconde (bit/s), Kilobits par seconde (Kbits/s), mégabits par seconde (Mbits/s) ou Gigabits par seconde (Gbits/s).

## Codes d’état

Le graphique Codes d’état se compose de lignes en couleur qui indiquent la fréquence à laquelle les codes de réponse HTTP se produisent sur une période spécifiée. Le côté gauche du graphique (axe y) indique la fréquence à laquelle un code d’état est retourné pour les requêtes, tandis que la partie inférieure du graphique (axe x) indique la progression du temps.

La liste des codes d’état s’affiche directement au-dessus du graphique. Cette liste indique chaque code d’état pouvant être inclus dans le graphique linéaire et le nombre actuel d’occurrences par seconde pour ce code d’état. Par défaut, une ligne s’affiche pour chacun de ces codes d’état dans le graphique. Toutefois, vous pouvez choisir de surveiller uniquement les codes d’état qui ont une importance spéciale pour votre configuration CDN. Pour ce faire, vous pouvez cocher uniquement les options de code d’état souhaitées et désactiver toutes les autres. Une fois que les codes d’état qui seront affichés dans le graphique vous conviennent, cliquez sur Actualiser le graphique. Les codes d’état écartés ne sont alors plus inclus dans le graphique.

> [AZURE.NOTE] L’option **Actualiser le graphique** efface le graphique. Ensuite, seuls les codes d’état sélectionnés s’affichent.

Chaque option de code d’état est décrite ci-dessous.

Nom | Description
-----|------------
Nombre total de correspondances par seconde | Détermine si le nombre total de requêtes par seconde pour la plateforme actuelle apparaît dans le graphique. Vous pouvez utiliser cette option comme indicateur de base pour déterminer le pourcentage du nombre total de correspondances que comprend un code d’état particulier.
2xx par seconde | Détermine si le nombre total de codes d’état 2xx (par exemple, 200, 201, 202, etc.) qui se produisent par seconde pour la plateforme actuelle apparaît dans le graphique. Ce type de code d’état indique que la requête a été correctement remise au client.
304 par seconde | Détermine si le nombre total de codes d’état 304 qui se produisent par seconde pour la plateforme actuelle apparaît dans le graphique. Ce code d’état indique que la ressource demandée n’a pas été modifiée depuis sa dernière récupération par le client HTTP.
3xx par seconde | Détermine si le nombre total de codes d’état 3xx (par exemple, 300, 301, 302, etc.) qui se produisent par seconde pour la plateforme actuelle apparaît dans le graphique. Cette option exclut les occurrences des codes d’état 304. Ce type de code d’état indique que la requête a généré une redirection.
403 par seconde | Détermine si le nombre total de codes d’état 403 qui se produisent par seconde pour la plateforme actuelle apparaît dans le graphique. Ce code d’état indique que la requête a été considérée comme non autorisée. L’une des causes possibles de ce code d’état est liée à un utilisateur non autorisé qui demande une ressource protégée par l’authentification basée sur les jetons.
404 par seconde | Détermine si le nombre total de codes d’état 404 qui se produisent par seconde pour la plateforme actuelle apparaît dans le graphique. Ce code d’état indique que la ressource demandée est introuvable.
4xx par seconde | Détermine si le nombre total de codes d’état 4xx (par exemple, 400, 401, 402, 405, etc.) qui se produisent par seconde pour la plateforme actuelle apparaît dans le graphique. Cette option exclut les occurrences des codes d’état 403 et 404. Ce code d’état indique que la ressource demandée n’a pas été remise au client.
5xx par seconde | Détermine si le nombre total de codes d’état 5xx (par exemple, 500, 501, 502, etc.) qui se produisent par seconde pour la plateforme actuelle apparaît dans le graphique.
Autre par seconde | Détermine si le nombre total d’occurrences de tous les autres codes d’état figure dans le graphique.

Vous pouvez également choisir de masquer temporairement les données consignées pour un code d’état spécifique. Vous pouvez effectuer cela à partir de la zone située directement sous le graphique en décochant l’option de code d’état souhaitée. Le code d’état sélectionné est immédiatement masqué dans le graphique. Le fait de cocher cette option de code d’état la fait apparaître à nouveau.

> [AZURE.NOTE] Les options en couleur situées directement sous le graphique affectent uniquement ce qui est affiché dans le graphique. Elles n’ont aucune incidence sur le fait que le graphique garde une trace de ce code d’état ou non.

## États du cache

Le graphique États du cache se compose de lignes en couleur qui indiquent la fréquence à laquelle certains types d’états du cache se produisent pendant une période spécifiée. Le côté gauche du graphique (axe y) indique la fréquence à laquelle un état du cache est retourné pour les requêtes, tandis que la partie inférieure du graphique (axe x) indique la progression du temps.

La liste des états du cache s’affiche directement au-dessus du graphique. Cette liste indique chaque état du cache pouvant être inclus dans le graphique linéaire et le nombre actuel d’occurrences par seconde pour cet état du cache. Par défaut, une ligne s’affiche pour chacun de ces états du cache dans le graphique. Toutefois, vous pouvez choisir de surveiller uniquement les états du cache qui ont une importance spéciale pour votre configuration CDN. Pour ce faire, vous pouvez cocher uniquement les options d’état du cache souhaitées et désactiver toutes les autres. Une fois que les états du cache qui seront affichés dans le graphique vous conviennent, cliquez sur Actualiser le graphique. Les codes d’état écartés ne sont alors plus inclus dans le graphique.

> [AZURE.NOTE] L’option **Actualiser le graphique** efface le graphique. Ensuite, seuls les états du cache sélectionnés s’affichent.

Vous pouvez également choisir de masquer temporairement les données consignées pour un code de réponse spécifique. Vous pouvez effectuer cela en décochant l’option de code de réponse souhaitée dans la zone située directement sous le graphique. Le code de réponse sélectionné est immédiatement masqué dans le graphique. Le fait de cocher cette option de code de réponse la fait apparaître à nouveau.

> [AZURE.NOTE] Les options en couleur situées directement sous le graphique affectent uniquement ce qui est affiché dans le graphique. Elles n’ont aucune incidence sur le fait que le graphique garde une trace de ce code d’état ou non.

## Connexions

Cette représentation graphique du nombre moyen de connexions par minute vous permet d’afficher le nombre établi de connexions à vos serveurs. Une connexion se compose de toutes les requêtes de ressource qui traversent notre CDN.

## Voir aussi
* [Vue d'ensemble d'Azure CDN](cdn-overview.md)
* [Remplacement du comportement HTTP par défaut à l’aide du moteur de règles](cdn-rules-engine.md)
* [Rapports HTTP avancés](cdn-advanced-http-reports.md)
* [Analyser les performances de serveurs Edge](cdn-edge-performance.md)

<!---HONumber=AcomDC_0302_2016-->