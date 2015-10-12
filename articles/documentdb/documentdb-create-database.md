<properties 
	pageTitle="Création d’une base de données DocumentDB NoSQL | Microsoft Azure" 
	description="Apprenez à créer des bases de données gérées avec le portail du service en ligne pour Azure DocumentDB, un service de gestion de base de données de documents NoSQL pour JSON. Essayez gratuitement dès aujourd’hui." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/28/2015" 
	ms.author="mimig"/>

# Création d’une base de données DocumentDB avec le portail Azure en version préliminaire

Pour utiliser Microsoft Azure DocumentDB, vous devez posséder un [compte DocumentDB](documentdb-create-account.md), une base de données, une collection et des documents. Cette rubrique décrit la procédure à suivre pour créer une base de données DocumentDB dans le portail Azure en version préliminaire.

![Capture d'écran montrant le bouton Parcourir, des comptes DocumentDB dans le panneau Parcourir et un compte DocumentDB dans le panneau Comptes DocumentDB](./media/documentdb-create-database/docdb-database-creation-1-3.png)

1.  Dans la barre de lancement du [portail Azure en version préliminaire](https://portal.azure.com/), cliquez sur **Comptes DocumentDB**. 

2.  Dans le panneau **Comptes DocumentDB**, sélectionnez le compte dans lequel une base de données DocumentDB sera ajoutée. Si vous n'avez aucun compte répertorié, vous devrez [créer un compte DocumentDB](documentdb-create-account.md).

3. Dans le panneau **Compte DocumentDB**, cliquez sur **Ajout d'une base de données**.

4. Dans le panneau **Ajout d’une base de données**, saisissez l'ID de votre nouvelle base de données. Lorsque le nom est validé, une coche verte s'affiche dans la case **ID**.

5. Cliquez sur **OK** en bas de l'écran pour créer la nouvelle base de données.

7. La nouvelle base de données apparaît désormais dans le filtre **Bases de données** dans le panneau **Compte DocumentDB**.
 
	![Capture d'écran de la nouvelle base de données dans le panneau Compte DocumentDB](./media/documentdb-create-database/docdb-database-creation-7.png)

## Autres méthodes de création d'une base de données DocumentDB

Les bases de données ne doivent pas nécessairement être créées avec la version préliminaire du portail. Vous pouvez également les créer avec les [kits de développement logiciel (SDK) DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Pour voir un exemple de code C# sur la création d’une base de données avec le kit de développement logiciel .NET DocumentDB, consultez le fichier [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) du projet CollectionManagement, disponible dans le référentiel [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) sur [GitHub.com](https://github.com).

## Étapes suivantes

Maintenant que vous disposez d'une base de données DocumentDB, l'étape suivante consiste à [créer une collection](documentdb-create-collection.md).

Une fois votre collection créée, vous pouvez [ajouter des documents](../documentdb-view-json-document-explorer.md) à l’aide de l’Explorateur de documents dans la version préliminaire du portail, [importer des documents](documentdb-import-data.md) dans la collection à l’aide de l’outil de migration de données DocumentDB, ou encore utiliser l’un des [kits de développement logiciel (SDK) DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx) pour effectuer des opérations CRUD. DocumentDB possède les kits de développement logiciel (SDK) de .NET, Java, Python, Node.js et de l’API JavaScript. Pour plus d’exemples de code .NET sur la création, le retrait, la mise à jour et la suppression des documents, consultez [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) dans le projet DocumentManagement du référentiel azure-documentdb-net sur GitHub.com.

Une fois que chaque collection contient des documents, vous pouvez utiliser [DocumentDB SQL](documentdb-sql-query.md) pour [exécuter des requêtes](documentdb-sql-query.md#executing-queries) sur vos documents à l’aide de l’[Explorateur de requête](documentdb-query-collections-query-explorer.md) dans la version préliminaire du portail, de l’[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de l’un des [kits de développement logiciel](https://msdn.microsoft.com/library/azure/dn781482.aspx).

<!---HONumber=Oct15_HO1-->