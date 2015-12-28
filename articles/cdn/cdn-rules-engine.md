<properties 
	pageTitle="CDN - Remplacement du comportement HTTP par défaut à l'aide du moteur de règles" 
	description="Le moteur de règles vous permet de personnaliser comment sont gérées les requêtes HTTP, telles que le blocage de la remise de certains types de contenu, la définition d'une stratégie de mise en cache et la modification des en-têtes HTTP." 
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
	ms.date="12/02/2015" 
	ms.author="casoper"/>

# Remplacement du comportement HTTP par défaut à l'aide du moteur de règles

## Vue d'ensemble

Le moteur de règles vous permet de personnaliser comment sont gérées les requêtes HTTP, telles que le blocage de la remise de certains types de contenu, la définition d'une stratégie de mise en cache et la modification des en-têtes HTTP. Ce didacticiel présente la création d'une règle qui modifie le comportement de mise en cache de ressources CDN.

> [AZURE.NOTE]Le moteur de règles est une fonctionnalité du niveau CDN Premium. Pour comparer les fonctionnalités CDN Standard et Premium, consultez [Vue d'ensemble du réseau de distribution de contenu (CDN) Azure](cdn-overview.md).

## Didacticiel

1. Dans le panneau de profil CDN, cliquez sur le bouton **Gérer**.

	![Bouton Gérer du panneau de profil CDN](./media/cdn-rules-engine/cdn-rules-manage-btn.png)
	
	Le portail de gestion CDN s'ouvre.
	
2. Cliquez sur l'onglet **HTTP volumineux**, puis **Moteur de règles**.
	
	Les options d'une nouvelle règle s'affichent.
	
	![Options de nouvelle règle CDN](./media/cdn-rules-engine/cdn-new-rule.png)

3. Entrez un nom dans la zone de texte **Nom / Description**.

4. Identifiez le type de requêtes auxquelles la règle s'applique. Par défaut, la condition de correspondance **Toujours** est sélectionnée. Pour ce didacticiel, vous allez utiliser **Toujours**, donc conservez cette option.

	![Condition de correspondance CDN](./media/cdn-rules-engine/cdn-request-type.png)
	
	>[AZURE.TIP]Il existe de nombreux types de condition de correspondance disponibles dans la liste déroulante. Cliquez sur l'icône d'information bleue à gauche de la condition de correspondance pour obtenir une explication détaillée de la condition sélectionnée.
	>
	>Pour obtenir la liste complète des conditions de correspondance, consultez [Informations sur les fonctionnalités et conditions de correspondance du moteur de règles](cdn-rules-engine-details.md#match-conditions).
	
5.  Cliquez sur le bouton **+** en regard de **Fonctionnalités** pour ajouter une fonctionnalité. Dans la liste déroulante située à gauche, sélectionnez **Forcer l'âge maximal interne**. Dans la zone de texte qui s'affiche, entrez **300**. Conservez les valeurs par défaut restantes.

	![Fonctionnalité CDN](./media/cdn-rules-engine/cdn-new-feature.png)

	>[AZURE.NOTE]Comme avec les conditions de correspondance, en cliquez sur l'icône d'information bleue à gauche de la nouvelle fonctionnalité pour obtenir plus d'informations sur cette fonctionnalité. Dans le cas de **Forcer l'âge maximal interne**, nous remplaçons les en-têtes **Cache-Control** et **Expires** de la ressource, afin de contrôler quand le nœud de périmètre CDN actualise la ressource à partir de l'origine. Notre exemple de 300 secondes signifie que le nœud de périmètre CDN met la ressource en cache pendant 5 minutes avant l'actualisation de la ressource à partir de son origine.
	>
	>Pour obtenir la liste complète des fonctionnalités, consultez [Informations sur les fonctionnalités et conditions de correspondance du moteur de règles](cdn-rules-engine-details.md#features).
	
6.  Cliquez sur le bouton **Ajouter** pour enregistrer la nouvelle règle. La nouvelle règle est en attente d'approbation. Une fois qu'elle a été approuvée, l'état passe de **XML en attente** à **XML actif**.

## Considérations

- L'ordre dans lequel plusieurs règles sont répertoriées affecte la façon dont elles sont gérées. Une règle ultérieure peut remplacer les actions spécifiées par une règle antérieure.

## Voir aussi
* [Informations sur les fonctionnalités et conditions de correspondance du moteur de règles](cdn-rules-engine-details.md)
* [Vue d'ensemble d'Azure CDN](cdn-overview.md)
* [Statistiques en temps réel dans Microsoft Azure CDN](cdn-real-time-stats.md)
* [Rapports HTTP avancés](cdn-advanced-http-reports.md)
* [Analyser les performances de serveurs Edge](cdn-edge-performance.md)


	

<!---HONumber=AcomDC_1217_2015-->