<properties
	pageTitle="Explorateur de requêtes DocumentDB : Un éditeur de requête SQL | Microsoft Azure"
	description="Découvrez l’Explorateur de requêtes DocumentDB, un éditeur de requête SQL dans le portail Azure destiné à l’écriture de requêtes SQL et à leur exécution dans une collection DocumentDB NoSQL."
	keywords="écriture de requêtes sql, éditeur de requête sql"
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="anhoh"/>

# Écrire, modifier et exécuter des requêtes SQL pour DocumentDB à l’aide de l’Explorateur de requêtes 

Cet article fournit une vue d’ensemble de l’Explorateur de requêtes de [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/), un outil du portail Azure qui permet d’écrire, de modifier et d’exécuter des requêtes SQL sur une [collection DocumentDB](documentdb-create-collection.md).

1. Dans la barre de lancement du portail Azure, cliquez sur **DocumentDB (NoSQL)**. Si l’option **DocumentDB (NoSQL)** n’est pas affichée, cliquez sur **Plus de services**, puis sur **DocumentDB (NoSQL)**.

2. Dans le menu Ressource, cliquez sur **Explorateur de requêtes**.

	![Capture d’écran du portail Azure avec l’Explorateur de requêtes mis en surbrillance](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)

3. Dans le panneau **Explorateur de requêtes**, sélectionnez les **Bases de données** et **Collections** à interroger dans les listes déroulantes, puis saisissez la requête à exécuter.

    Les listes déroulantes **Bases de données** et **Collections** sont préremplies en fonction du contexte de lancement de l’Explorateur de requêtes.

    La requête par défaut `SELECT TOP 100 * FROM c` est indiquée. Vous pouvez l’accepter ou créer la vôtre à l’aide du langage de requête SQL décrit dans [l’Aide-mémoire pour les requêtes SQL](documentdb-sql-query-cheat-sheet.md) ou dans l’article [Requête SQL et syntaxe SQL dans DocumentDB](documentdb-sql-query.md).

    Cliquez sur **Exécuter la requête** pour afficher les résultats.

	![Capture d’écran montant l’écriture de requêtes SQL dans l’Explorateur de requêtes, un éditeur de requête SQL](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)

4. Le panneau **Résultats** affiche la sortie de la requête.

	![Capture d’écran des résultats de l’écriture de requêtes SQL dans l’Explorateur de requêtes](./media/documentdb-query-collections-query-explorer/queryresults1.png)

## Utiliser les résultats

L'Explorateur de requête renvoie par défaut les résultats par groupe de 100. Si la requête renvoie plus de 100 résultats, utilisez les commandes **Page suivante** et **Page précédente** pour naviguer dans les résultats.

![Capture d’écran de la pagination dans l’Explorateur de requête](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

Si les requêtes ont abouti, le volet **Informations** indique des mesures telles que les frais de requête, le nombre d’aller-retour effectués par la requête, l’ensemble de résultats actuellement affiché et s’il existe d’autres résultats, qui sont accessibles via la commande **Page suivante**, comme indiqué précédemment.

![Capture d’écran des informations de requête de l’Explorateur de requête](./media/documentdb-query-collections-query-explorer/queryinformation.png)

## Utiliser plusieurs requêtes

Si vous utilisez plusieurs requêtes et que vous souhaitez basculer rapidement entre elles, vous pouvez entrer toutes les requêtes dans la zone de texte de la requête du panneau **Explorateur de requêtes**, mettre en surbrillance celle que vous souhaitez exécuter, puis cliquer sur **Exécuter la requête** pour afficher les résultats.

![Capture d’écran montrant l’écriture de plusieurs requêtes SQL dans l’Explorateur de requêtes (un éditeur de requête SQL) et mettant en surbrillance et exécutant des requêtes individuelles](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

## Ajouter des requêtes à partir d’un fichier dans l’éditeur de requête SQL

Vous pouvez charger le contenu d’un fichier existant à l’aide de la commande **Charger le fichier**.

![Capture d’écran montrant comment charger des requêtes SQL à partir d’un fichier dans l’Explorateur de requêtes à l’aide de l’option Charger le fichier](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

## Résolution des problèmes

Si une requête se termine avec des erreurs, l’Explorateur de requêtes affiche une liste d’erreurs qui peut aider à la résolution des problèmes.

![Capture d’écran des erreurs de requête de l’Explorateur de requête](./media/documentdb-query-collections-query-explorer/queryerror.png)

## Exécuter des requêtes SQL DocumentDB en dehors du portail

L’Explorateur de requêtes du portail Azure permet simplement d’exécuter des requêtes SQL dans DocumentDB. Vous pouvez également exécuter des requêtes SQL à l’aide de [l’API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou des [Kits de développement logiciel (SDK) clients](documentdb-sdk-dotnet.md). Pour plus d’informations sur l’utilisation de ces méthodes, consultez la section [Exécution de requêtes SQL](documentdb-sql-query.md#executing-sql-queries)

## Étapes suivantes

Pour plus d’informations sur la grammaire SQL DocumentDB prise en charge dans l’Explorateur de requêtes, consultez l’article [Requête SQL et syntaxe SQL dans DocumentDB](documentdb-sql-query.md) ou imprimez [l’Aide-mémoire pour les requêtes SQL](documentdb-sql-query-cheat-sheet.md). Vous pouvez également expérimenter le [Query Playground](https://www.documentdb.com/sql/demo) où vous pouvez tester des requêtes en ligne en utilisant un exemple de jeu de données.

<!---HONumber=AcomDC_0831_2016-->