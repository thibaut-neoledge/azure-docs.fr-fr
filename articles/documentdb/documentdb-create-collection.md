<properties 
	pageTitle="Création d’une collection de base de données DocumentDB | Microsoft Azure" 
	description="Apprenez à créer des collections avec le portail du service en ligne pour Azure DocumentDB, un service de gestion de base de données de documents NoSQL géré pour JSON. Essayez gratuitement dès aujourd’hui." 
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

# Création d’une collection DocumentDB

Pour utiliser Microsoft Azure DocumentDB, vous devez posséder un [compte DocumentDB](documentdb-create-account.md), une [base de données](documentdb-create-database.md), une collection et des documents. Cette rubrique décrit la procédure à suivre pour créer une collection DocumentDB dans le portail Azure en version préliminaire.

![Capture d'écran montrant l'option Comptes DocumentDB dans la barre de lancement, le compte dans le panneau Comptes DocumentDB et la base de données dans le panneau Compte DocumentDB, dans le filtre Bases de données](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

1.  Dans la barre de lancement du [portail Azure en version préliminaire](https://portal.azure.com/), cliquez sur **Comptes DocumentDB**. 

2.  Dans le panneau **Comptes DocumentDB**, sélectionnez le compte dans lequel une collection sera ajoutée. Si vous n'avez aucun compte répertorié, vous devrez [créer un compte DocumentDB](documentdb-create-account.md).

3. Dans le panneau **Compte DocumentDB** pour le compte sélectionné, faites défiler jusqu'au filtre **Bases de données**, puis sélectionnez la base de données dans laquelle ajouter une collection.
    
4. Dans le panneau **Base de données**, cliquez sur **Ajout des collections**.

	![Capture d'écran montrant le bouton Ajouter la collection dans le panneau Base de données, les paramètres dans le panneau Ajout de la collection et le bouton OK](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

5. Dans le panneau **Ajout d’une collection**, saisissez l’ID de votre nouvelle collection. Lorsque le nom est validé, une coche verte s'affiche dans la case ID.

6. Sélectionnez un niveau de tarification pour la nouvelle collection. Chaque collection que vous créez est une entité facturable. Pour plus d'informations sur les niveaux de performances disponibles, consultez [Niveaux de performance dans DocumentDB](documentdb-performance-levels.md).

7. Sélectionnez l'une des **stratégies d'indexation** suivantes.

	- **Par défaut**. Cette stratégie est préférable si vous exécutez des requêtes d’efficacité sur des chaînes et des requêtes ORDER BY, de plage et d’efficacité sur des nombres. Cette stratégie dispose d'une surcharge de stockage d'index inférieure à **Plage**.
	- **Hachage**. Cette stratégie est préférable si vous exécutez des requêtes d'efficacité sur des nombres et des chaînes. Cette stratégie dispose de la plus faible surcharge de stockage d'index.
	- **Plage**. Cette stratégie est préférable lorsque vous exécutez des requêtes ORDER BY, de plage et d’égalité sur des nombres et des chaînes. Cette stratégie dispose d'une surcharge de stockage d'index supérieure à **Par défaut** ou **Hachage**.

	Pour plus d'informations sur les stratégies d'indexation, consultez la rubrique [Stratégies d'indexation DocumentDB](documentdb-indexing-policies.md).

8. Cliquez sur **OK** en bas de l'écran pour créer la nouvelle collection.


9. La nouvelle collection s'affiche maintenant dans le filtre **Collections** dans le **Base de données**.
 
	![Capture d'écran de la nouvelle collection dans le panneau Base de données](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## Autres méthodes de création d'une collection DocumentDB

Les collections ne doivent pas nécessairement être créées avec la version préliminaire du portail. Vous pouvez également les créer avec les [kits de développement logiciel (SDK) DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Pour voir un exemple de code C# sur la création d’une collection avec le kit de développement logiciel .NET DocumentDB, consultez le fichier [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) du projet CollectionManagement, disponible dans le référentiel [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) sur [GitHub.com](https://github.com).

## Étapes suivantes

Maintenant que vous avez une collection, l'étape suivante consiste à ajouter ou importer des documents dans la collection. Lorsqu'il s'agit d'ajouter des documents à une collection, vous avez le choix :

- Vous pouvez [ajouter des documents](../documentdb-view-json-document-explorer.md) à l’aide de l’Explorateur de documents, dans la version préliminaire du portail.
- Vous pouvez [importer des documents et des données](documentdb-import-data.md) à l'aide de l'outil de migration de données DocumentDB qui vous permet d'importer des fichiers JSON et CSV ainsi que des données depuis SQL Server, MongoDB, le stockage de table Azure et d’autres collections DocumentDB. 
- Vous pouvez également ajouter des documents en vous aidant des [kits de développement logiciel (SDK) DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB possède les kits de développement logiciel (SDK) de .NET, Java, Python, Node.js et de l’API JavaScript. Le fichier [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) du projet DocumentManagement, disponible dans le référentiel [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) sur [GitHub.com](https://github.com), montre des opérations CRUD sur des documents en utilisant le kit de développement logiciel .NET DocumentDB.

Une fois que chaque collection contient des documents, vous pouvez utiliser [DocumentDB SQL](documentdb-sql-query.md) pour [exécuter des requêtes](documentdb-sql-query.md#executing-queries) sur vos documents à l’aide de l’[Explorateur de requête](documentdb-query-collections-query-explorer.md) dans la version préliminaire du portail, de l’[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de l’un des [kits de développement logiciel](https://msdn.microsoft.com/library/azure/dn781482.aspx).

<!---HONumber=Nov15_HO4-->