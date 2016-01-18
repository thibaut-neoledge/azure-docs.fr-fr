<properties 
	pageTitle="Purger un point de terminaison CDN Azure" 
	description="Découvrez comment vider tout le contenu mis en cache à partir d’un point de terminaison CDN." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="casoper"/>
	
# Purger un point de terminaison CDN Azure

## Vue d’ensemble 

Les nœuds de périmètre CDN Azure mettent en cache les éléments multimédias jusqu’à l’expiration de leur durée de vie. Une fois la durée de vie de l’élément multimédia expirée, lorsqu’un client demande l’élément multimédia à partir du nœud de périmètre, ce dernier récupère une nouvelle copie mise à jour de l’élément pour répondre à la demande du client et actualiser le cache.

Il est parfois souhaitable de vider le contenu mis en cache sur tous les nœuds de périmètre et de tous les forcer à récupérer de nouveaux éléments multimédias mis à jour. Ce besoin peut être dû à des mises à jour de votre application web, ou à la nécessité de mettre à jour rapidement les éléments multimédias qui contiennent des informations incorrectes.

Ce didacticiel vous guide dans le processus de vidage des éléments multimédias de tous les nœuds d’un point de terminaison.

## Procédure pas à pas

1. Dans le [portail Azure](http://portal.azure.com), recherchez le profil CDN qui contient le point de terminaison que vous souhaitez vider.

2. Dans le panneau du profil CDN, cliquez sur le bouton de vidage.
	
	![Panneau du profil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
	
	Le panneau correspondant s’ouvre.
	
	![Panneau de vidage CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
	
3. Dans le panneau de vidage, sélectionnez l’adresse de service que vous souhaitez vider dans la liste déroulante des URL.

	![Formulaire de vidage](./media/cdn-purge-endpoint/cdn-purge-form.png)
	
	> [AZURE.NOTE]Vous pouvez également accéder au panneau de vidage en cliquant sur le bouton **Vider** dans le panneau des points de terminaison CDN. Dans ce cas, le champ **URL** sera déjà renseigné avec l’adresse de service de ce point de terminaison spécifique.
	
4. Sélectionnez les éléments multimédias que vous souhaitez vider sur les nœuds de périmètre. Si vous souhaitez effacer tous les éléments multimédias, cochez la case **Vider tout**. Sinon, entrez le chemin d’accès complet de chaque élément multimédia que vous souhaitez vider (par exemple, */pictures/kitten.png*) dans la zone de texte **Chemin d’accès**.

	> [AZURE.TIP]Après avoir saisi du texte, d’autres zones de texte **Chemin d’accès** s’afficheront pour vous permettre de créer une liste de plusieurs éléments multimédias. Vous pouvez supprimer des éléments multimédias de la liste en cliquant sur le bouton points de suspension (...).
	>
	> Les chemins d’accès doivent se présenter sous la forme d’une URL relative. L’astérisque (*) peut être utilisé comme caractère générique.
	
5. Cliquez sur le bouton **Vider**.

	![Bouton Vider](./media/cdn-purge-endpoint/cdn-purge-button.png)
	

## Voir aussi
[Référence API REST du CDN Azure - Vider ou pré-charger un point de terminaison](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0107_2016-->