<properties
	pageTitle="Remplacement du comportement HTTP par défaut dans Azure CDN à l’aide du moteur de règles | Microsoft Azure"
	description="Le moteur de règles vous permet de personnaliser la manière dont Azure CDN gère les requêtes HTTP, telles que le blocage de la remise de certains types de contenu, la définition d’une stratégie de mise en cache et la modification des en-têtes HTTP."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="casoper"/>

# Remplacement du comportement HTTP par défaut à l'aide du moteur de règles

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## Vue d'ensemble

Le moteur de règles vous permet de personnaliser comment sont gérées les requêtes HTTP, telles que le blocage de la remise de certains types de contenu, la définition d’une stratégie de mise en cache et la modification des en-têtes HTTP. Ce didacticiel présente la création d’une règle qui modifie le comportement de mise en cache des ressources CDN. Du contenu vidéo est aussi disponible dans la section « [Voir aussi](#see-also) ».

## Didacticiel

1. Dans le panneau de profil CDN, cliquez sur le bouton **Gérer**.

	![Bouton de gestion du panneau de profil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

	Le portail de gestion CDN s'ouvre.

2. Cliquez sur l'onglet **HTTP volumineux**, puis **Moteur de règles**.

	Les options d'une nouvelle règle s'affichent.

	![Options de nouvelle règle CDN](./media/cdn-rules-engine/cdn-new-rule.png)

	>[AZURE.IMPORTANT] L'ordre dans lequel plusieurs règles sont répertoriées affecte la façon dont elles sont gérées. Une règle ultérieure peut remplacer les actions spécifiées par une règle antérieure.
	
3. Entrez un nom dans la zone de texte **Nom / Description**.

4. Identifiez le type de requêtes auxquelles la règle s'applique. Par défaut, la condition de correspondance **Toujours** est sélectionnée. Pour ce didacticiel, vous allez utiliser **Toujours**, donc conservez cette option.

	![Condition de correspondance CDN](./media/cdn-rules-engine/cdn-request-type.png)

	>[AZURE.TIP] Il existe de nombreux types de condition de correspondance disponibles dans la liste déroulante. Cliquez sur l'icône d'information bleue à gauche de la condition de correspondance pour obtenir une explication détaillée de la condition sélectionnée.
	>
	>Pour obtenir la liste complète des conditions de correspondance, consultez [Informations sur les fonctionnalités et conditions de correspondance du moteur de règles](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_0).

5.  Cliquez sur le bouton **+** en regard de **Fonctionnalités** pour ajouter une fonctionnalité. Dans la liste déroulante située à gauche, sélectionnez **Forcer l'âge maximal interne**. Dans la zone de texte qui s'affiche, entrez **300**. Conservez les valeurs par défaut restantes.

	![Fonctionnalité CDN](./media/cdn-rules-engine/cdn-new-feature.png)

	>[AZURE.NOTE] Comme avec les conditions de correspondance, en cliquez sur l'icône d'information bleue à gauche de la nouvelle fonctionnalité pour obtenir plus d'informations sur cette fonctionnalité. Dans le cas de **Forcer l'âge maximal interne**, nous remplaçons les en-têtes **Cache-Control** et **Expires** de la ressource, afin de contrôler quand le nœud de périmètre CDN actualise la ressource à partir de l'origine. Notre exemple de 300 secondes signifie que le nœud de périmètre CDN met la ressource en cache pendant 5 minutes avant l'actualisation de la ressource à partir de son origine.
	>
	>Pour obtenir la liste complète des fonctionnalités, consultez [Informations sur les fonctionnalités et conditions de correspondance du moteur de règles](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1).

6.  Cliquez sur le bouton **Ajouter** pour enregistrer la nouvelle règle. La nouvelle règle est en attente d'approbation. Une fois qu'elle a été approuvée, l'état passe de **XML en attente** à **XML actif**.

	>[AZURE.IMPORTANT] Les modifications de règles peuvent prendre jusqu’à 90 minutes avant d’être propagées à travers le CDN.

## Voir aussi
* [Azure Friday : les nouvelles fonctionnalités Premium puissantes du CDN Azure](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vidéo)
* [Informations sur les fonctionnalités et conditions de correspondance du moteur de règles](https://msdn.microsoft.com/library/mt757336.aspx)

<!---HONumber=AcomDC_0803_2016-->