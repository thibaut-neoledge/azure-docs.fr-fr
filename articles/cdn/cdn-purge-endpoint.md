<properties
	pageTitle="Purger un point de terminaison CDN Azure"
	description="Découvrez comment vider tout le contenu mis en cache à partir d’un point de terminaison CDN."
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
	ms.date="05/11/2016"
	ms.author="casoper"/>

# Purger un point de terminaison CDN Azure

## Vue d’ensemble

Les nœuds de périmètre CDN Azure mettent en cache les éléments multimédias jusqu’à l’expiration de leur durée de vie. Une fois la durée de vie de l’élément multimédia expirée, lorsqu’un client demande l’élément multimédia à partir du nœud de périmètre, ce dernier récupère une nouvelle copie mise à jour de l’élément pour répondre à la demande du client et actualiser le cache.

Il est parfois souhaitable de vider le contenu mis en cache sur tous les nœuds de périmètre et de tous les forcer à récupérer de nouveaux éléments multimédias mis à jour. Ce besoin peut être dû à des mises à jour de votre application web, ou à la nécessité de mettre à jour rapidement les éléments multimédias qui contiennent des informations incorrectes.

> [AZURE.TIP] Notez que le vidage efface uniquement le contenu mis en cache sur les serveurs de périmètre CDN. Les caches en aval, tels que les serveurs proxy et les caches de navigateurs locaux, peuvent toujours contenir une copie mise en cache du fichier. Il est important de s’en rappeler au moment où vous définissez la durée de vie d’un fichier. Vous pouvez forcer un client en aval à demander la dernière version de votre fichier en lui donnant un nom unique chaque fois que vous le mettez à jour ou en tirant parti de la [mise en cache de chaîne de requête](cdn-query-string.md).

Ce didacticiel vous guide dans le processus de vidage des éléments multimédias de tous les nœuds d’un point de terminaison.

## Procédure pas à pas

1. Dans le [portail Azure](https://portal.azure.com), recherchez le profil CDN qui contient le point de terminaison que vous souhaitez vider.

2. Dans le panneau du profil CDN, cliquez sur le bouton de vidage.

	![Panneau du profil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)

	Le panneau correspondant s’ouvre.

	![Panneau de vidage CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)

3. Dans le panneau de vidage, sélectionnez l’adresse de service que vous souhaitez vider dans la liste déroulante des URL.

	![Formulaire de vidage](./media/cdn-purge-endpoint/cdn-purge-form.png)

	> [AZURE.NOTE] Vous pouvez également accéder au panneau de vidage en cliquant sur le bouton **Vider** dans le panneau des points de terminaison CDN. Dans ce cas, le champ **URL** sera déjà renseigné avec l’adresse de service de ce point de terminaison spécifique.

4. Sélectionnez les éléments multimédias que vous souhaitez vider sur les nœuds de périmètre. Si vous souhaitez effacer tous les éléments multimédias, cochez la case **Vider tout**. Sinon, tapez le chemin complet de chaque élément multimédia que vous souhaitez vider (par exemple, `/pictures/kitten.png`) dans la zone de texte **Chemin d’accès**.

	> [AZURE.TIP] Après avoir saisi du texte, d’autres zones de texte **Chemin d’accès** s’afficheront pour vous permettre de créer une liste de plusieurs éléments multimédias. Vous pouvez supprimer des éléments multimédias de la liste en cliquant sur le bouton points de suspension (...).
	>
	> Les chemins doivent être une URL relative qui satisfait à l’[expression régulière](https://msdn.microsoft.com/library/az24scfc.aspx) suivante : `^\/(?:[a-zA-Z0-9-_.\u0020]+\/)**$";`. Pour le **CDN Azure fourni par Verizon** (Standard et Premium), il est possible d’utiliser un astérisque (*) comme caractère générique (par exemple, `/music/*`). Les caractères génériques et l’option **Vider tout** ne sont pas autorisés avec le **CDN Azure fourni par Akamai**.
	
5. Cliquez sur le bouton **Vider**.

	![Bouton Vider](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [AZURE.IMPORTANT] Le traitement des demandes de vidage prennent environ 2 à 3 minutes avec le **CDN Azure fourni par Verizon** (Standard et Premium) et environ 7 minutes avec le **CDN Azure fourni par Akamai**. Le CDN Azure impose une limite de 50 demandes de vidage simultanées à un moment donné.

## Voir aussi
- [Préchargement d’éléments multimédias sur un point de terminaison CDN Azure](cdn-preload-endpoint.md)
- [Référence API REST du CDN Azure - Vider ou pré-charger un point de terminaison](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0518_2016-->