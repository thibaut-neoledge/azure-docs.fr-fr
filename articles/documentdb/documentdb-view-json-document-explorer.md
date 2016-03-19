<properties
	pageTitle="Explorateur de documents DocumentDB pour afficher du code JSON | Microsoft Azure"
	description="Découvrez l’Explorateur de documents DocumentDB, un outil du portail Azure qui permet d’afficher, de modifier, de créer et de charger des documents JSON avec DocumentDB, une base de données de documents NoSQL."
    keywords="afficher json"
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
	ms.topic="get-started-article"
	ms.date="02/23/2016"
	ms.author="anhoh"/>

# Afficher, modifier, créer et charger des documents JSON à l’aide de l’Explorateur de documents DocumentDB

Cet article fournit une vue d’ensemble de l’Explorateur de documents [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/), un outil du portail Azure qui vous permet d’afficher, de modifier, de créer, de charger et de filtrer des documents JSON avec DocumentDB.

## Lancer l’Explorateur de documents

1. Dans la barre de lancement du portail Azure, cliquez sur **Comptes DocumentDB**. Si l’option **Comptes DocumentDB** n’est pas affichée, cliquez sur **Parcourir**, puis sur **Comptes DocumentDB**.

2. En haut du panneau **Compte DocumentDB**, cliquez sur **Explorateur de documents**.
 
	![Capture d’écran de la commande de l’Explorateur de documents](./media/documentdb-view-JSON-document-explorer/documentexplorercommand.png)

 	>[AZURE.NOTE] L’Explorateur de requêtes s’affiche également dans les panneaux de base de données et de collection.

    Dans le panneau **Explorateur de documents**, les listes déroulantes **Bases de données** et **Collections** sont préremplies en fonction du contexte de lancement de l’Explorateur de requêtes.

	![Capture d’écran du panneau Explorateur de documents](./media/documentdb-view-JSON-document-explorer/documentexplorerinitial.png)

## Créer un document

1. [Lancez l’Explorateur de documents](#launch-document-explorer).

2. Dans le panneau **Explorateur de documents**, cliquez sur **Créer un document**.

    Un extrait de code JSON minimal est fourni dans le panneau **Document**.

	![Capture d’écran de la création de documents dans l’Explorateur de documents où vous pouvez afficher et modifier du code JSON](./media/documentdb-view-JSON-document-explorer/createdocument.png)

2. Dans le panneau **Document**, tapez ou collez le contenu du document JSON que vous souhaitez créer, puis cliquez sur **Enregistrer** pour valider votre document dans la base de données et la collection spécifiées dans le panneau **Explorateur de documents**.

	![Capture d’écran de la commande Enregistrer de l’Explorateur de documents](./media/documentdb-view-JSON-document-explorer/savedocument1.png)

	> [AZURE.NOTE] Si vous ne fournissez pas de propriété « id », l'Explorateur de documents en ajoute automatiquement une et génère un GUID comme valeur d'ID.

    Si vous avez déjà des données issues de fichiers JSON, MongoDB, SQL Server, fichiers CSV, stockage Azure Table, Amazon DynamoDB, HBase ou autres collections DocumentDB, vous pouvez utiliser les [outils de migration de données](documentdb-import-data.md) DocumentDB pour importer rapidement vos données.

## Modifier un document

1. [Lancez l’Explorateur de documents](#launch-document-explorer).

2. Pour modifier un document existant, sélectionnez-le dans le panneau **Explorateur de documents**, modifiez-le dans le panneau **Document**, puis cliquez sur **Enregistrer**.

    ![Capture d’écran de la fonctionnalité de modification des documents dans l’Explorateur de documents utilisée pour afficher du code JSON](./media/documentdb-view-JSON-document-explorer/editdocument.png)

    Si vous modifiez un document et décidez d’ignorer l’ensemble des modifications, cliquez simplement sur **Ignorer** dans le panneau **Document** et confirmez l’action de rejet. L’état précédent du document est alors rechargé.

    ![Capture d’écran de la commande Ignorer de l’Explorateur de documents](./media/documentdb-view-JSON-document-explorer/discardedit.png)

## Supprimer un document

1. [Lancez l’Explorateur de documents](#launch-document-explorer).

2. Dans l’**Explorateur de documents**, sélectionnez le document, cliquez sur **Supprimer**, puis confirmez la suppression. Après la confirmation, le document est immédiatement supprimé de la liste de l’Explorateur de documents.

	![Capture d’écran de la commande Supprimer de l’Explorateur de documents](./media/documentdb-view-JSON-document-explorer/deletedocument.png)

## Utiliser des documents JSON

L’Explorateur de documents vérifie que les documents nouveaux ou modifiés contiennent du code JSON valide. Vous pouvez même afficher les erreurs JSON en pointant sur la section incorrecte pour obtenir plus d’informations sur l’erreur de validation.

![Capture d’écran de l’Explorateur de documents avec mise en surbrillance du code JSON non valide](./media/documentdb-view-JSON-document-explorer/invalidjson1.png)

En outre, l'Explorateur de documents vous empêche d'enregistrer un document avec un contenu JSON non valide.

![Capture d’écran de l’Explorateur de documents avec erreur d’enregistrement JSON non valide](./media/documentdb-view-JSON-document-explorer/invalidjson2.png)

Pour finir, vous pouvez facilement afficher les propriétés du document actuellement chargé en cliquant sur la commande **Propriétés**.

![Capture d’écran de la vue des propriétés de document dans l’Explorateur de documents](./media/documentdb-view-JSON-document-explorer/documentproperties.png)

> [AZURE.NOTE] La propriété timestamp (\_ts) est représentée en interne comme heure d'époque, mais l'Explorateur de documents affiche la valeur dans un format GMT lisible.

## Filtrer les documents
L’Explorateur de documents prend en charge un certain nombre d’options de navigation et de paramètres avancés.

Par défaut, l’Explorateur de documents charge les 100 premiers documents dans la collection sélectionnée, en fonction de leur date de création, de la plus ancienne à la plus récente. Vous pouvez charger des documents supplémentaires (par lots de 100) en sélectionnant l’option **Charger plus** en bas du panneau Explorateur de documents. Vous pouvez choisir les documents à charger à l’aide de la commande **Filtrer**.

1. [Lancez l’Explorateur de documents](#launch-document-explorer).

2. En haut du panneau **Explorateur de documents**, cliquez sur **Filtrer**.

    ![Capture d’écran des paramètres de filtre de l’Explorateur de documents](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings.png)
  
3.  Les paramètres de filtre s’affichent au-dessous de la barre de commandes. Dans les paramètres de filtre, indiquez une clause WHERE et/ou une clause ORDER BY, puis cliquez sur **Filtrer**.

	![Capture d’écran du panneau Paramètres de l’Explorateur de documents](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings2.png)

	L’Explorateur de documents actualise automatiquement les résultats avec les documents qui correspondent à la requête de filtre. Pour plus d’informations sur la grammaire SQL DocumentDB, consultez l’article [Requête SQL et syntaxe SQL dans DocumentDB](documentdb-sql-query.md) ou imprimez l’[Aide-mémoire pour les requêtes SQL](documentdb-sql-query-cheat-sheet.md).

    Vous pouvez utiliser les zones de liste déroulante **Base de données** et **Collection** pour modifier facilement la collection à partir de laquelle les documents sont affichés actuellement sans avoir à fermer, puis à relancer l’Explorateur de documents.

    L'Explorateur de documents prend également en charge le filtrage de l'ensemble de documents actuellement chargé par leur propriété ID. Tapez simplement une valeur dans la zone Filtrer par ID de la section Documents.

	![Capture d’écran de l’Explorateur de documents avec le filtre en surbrillance](./media/documentdb-view-JSON-document-explorer/documentexplorerfilter.png)

	Les résultats de la liste de l’Explorateur de documents sont alors filtrés en fonction de vos critères.

	![Capture d’écran de l’Explorateur de documents avec les résultats filtrés](./media/documentdb-view-JSON-document-explorer/documentexplorerfilterresults.png)

	> [AZURE.IMPORTANT] La fonctionnalité de filtre de l’Explorateur de documents filtre uniquement l’ensemble de documents ***actuellement*** chargé et n’exécute pas de requête sur la collection actuellement sélectionnée.

4. Pour actualiser la liste des documents chargés par l’Explorateur de documents, cliquez sur **Actualiser** en haut du panneau.

	![Capture d’écran de la commande Actualiser de l’Explorateur de documents](./media/documentdb-view-JSON-document-explorer/documentexplorerrefresh.png)

## Ajouter des documents en bloc

L’Explorateur de documents prend en charge l’ingestion en bloc d’un ou de plusieurs documents JSON existants, jusqu’à 100 fichiers JSON par opération de chargement.

1. [Lancez l’Explorateur de documents](#launch-document-explorer).

2. Pour démarrer le processus de chargement, cliquez sur **Charger un document**.

	![Capture d’écran de la fonction d’ingestion en bloc de l’Explorateur de documents](./media/documentdb-view-JSON-document-explorer/uploaddocument1.png)

    Le panneau **Charger un document** s’ouvre.

2. Cliquez sur le bouton Parcourir pour ouvrir une fenêtre de l’Explorateur de fichiers, sélectionnez un ou plusieurs documents JSON à charger, puis cliquez sur **Ouvrir**.

	![Capture d’écran du processus d’ingestion en bloc de l’Explorateur de documents](./media/documentdb-view-JSON-document-explorer/uploaddocument2.png)

	> [AZURE.NOTE] L'Explorateur de documents prend actuellement en charge jusqu'à 100 documents JSON par opération de téléchargement.

3. Une fois que vous êtes satisfait de votre sélection, cliquez sur le bouton **Télécharger**. Les documents sont automatiquement ajoutés à la grille de l'Explorateur de documents et les résultats du téléchargement sont affichés à mesure que l'opération progresse. Les échecs d'importation sont signalés pour chaque fichier.

	![Capture d’écran des résultats de l’ingestion en bloc de l’Explorateur de documents](./media/documentdb-view-JSON-document-explorer/uploaddocument3.png)

4. Une fois l’opération terminée, vous pouvez sélectionner jusqu’à 100 autres documents à charger.

## Utiliser des documents JSON en dehors du portail

L’Explorateur de documents du portail Azure permet simplement d’utiliser des documents dans DocumentDB. Vous pouvez également utiliser des documents à l’aide de l’[API REST](https://msdn.microsoft.com/library/azure/mt489082.aspx) ou des [Kits de développement logiciel (SDK) clients](documentdb-sdk-dotnet.md). Pour obtenir un exemple de code, consultez les [exemples de document du Kit de développement logiciel (SDK) .NET](documentdb-dotnet-samples.md#document-examples) et les [exemples de document du Kit de développement logiciel (SDK) Node.js](documentdb-nodejs-samples.md#document-examples).

Si vous devez importer ou migrer des fichiers provenant d’une autre source (fichiers JSON, MongoDB, SQL Server, fichiers CSV, stockage de tables Azure, Amazon DynamoDB ou HBase), vous pouvez utiliser l’[outil de migration de données](documentdb-import-data.md) de DocumentDB pour importer rapidement vos données dans DocumentDB.

## Étapes suivantes

Pour plus d’informations sur la grammaire SQL DocumentDB prise en charge dans l’Explorateur de documents, consultez l’article [Requête SQL et syntaxe SQL dans DocumentDB](documentdb-sql-query.md) ou imprimez l’[Aide-mémoire pour les requêtes SQL](documentdb-sql-query-cheat-sheet.md).

Le [parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/documentdb/) est également une ressource utile pour vous guider lorsque vous en apprenez plus sur DocumentDB.

<!---HONumber=AcomDC_0224_2016-->