<properties 
	pageTitle="Création d’une collection de base de données DocumentDB | Microsoft Azure" 
	description="Apprenez à créer des collections de documents JSON avec le portail du service en ligne pour Azure DocumentDB, une base de données de documents NoSQL basée sur le cloud. Essayez gratuitement dès aujourd’hui." 
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
	ms.date="12/18/2015" 
	ms.author="mimig"/>

# Création d’une collection DocumentDB

Pour utiliser Microsoft Azure DocumentDB, vous devez posséder un [compte DocumentDB](documentdb-create-account.md), une [base de données](documentdb-create-database.md), une collection et des documents. Cette rubrique montre comment créer une collection DocumentDB dans le portail Azure.

Vous ne savez pas vraiment ce qu’est une collection ? Consultez [Qu’est-ce qu’une collection DocumentDB ?](#what-is-a-documentdb-collection)

![Capture d'écran montrant l'option Comptes DocumentDB dans la barre de lancement, le compte dans le panneau Comptes DocumentDB et la base de données dans le panneau Compte DocumentDB, dans le filtre Bases de données](./media/documentdb-create-collection/docdb-database-creation-1-3.png)

1.  Dans la barre de lancement du [portail Azure](https://portal.azure.com/), cliquez sur **Comptes DocumentDB**. 

2.  Dans le panneau **Comptes DocumentDB**, sélectionnez le compte dans lequel une collection sera ajoutée. Si vous n'avez aucun compte répertorié, vous devrez [créer un compte DocumentDB](documentdb-create-account.md).

3. Dans le panneau **Compte DocumentDB** pour le compte sélectionné, accédez au filtre **Bases de données**, puis sélectionnez la base de données dans laquelle ajouter une collection.
    
4. Dans le panneau **Base de données**, cliquez sur **Ajout des collections**.

	![Capture d’écran montrant le bouton Ajouter la collection dans le panneau Base de données, les paramètres dans le panneau Ajouter la collection et le bouton OK. Portail Azure pour DocumentDB - Créateur de bases de données basées sur le cloud pour les bases de données JSON NoSQL.](./media/documentdb-create-collection/docdb-collection-creation-4-7.png)

5. Dans le panneau **Ajout d’une collection**, saisissez l’ID de votre nouvelle collection. Les noms de collection doivent inclure entre 1 et 255 caractères et ne peuvent pas contenir `/ \ # ?` ni d’espace de fin. Lorsque le nom est validé, une coche verte s'affiche dans la case ID.

6. Sélectionnez un niveau de tarification pour la nouvelle collection. Chaque collection que vous créez est une entité facturable. Pour plus d'informations sur les niveaux de performances disponibles, consultez [Niveaux de performance dans DocumentDB](documentdb-performance-levels.md).

7. Sélectionnez l’une des **stratégies d’indexation** suivantes.

	- **Default**. Cette stratégie utilise l’indexation par hachage pour les chaînes et l’indexation par plage pour les nombres. Elle est idéale pour les requêtes equality sur des chaînes et les requêtes ORDER BY, range et equality sur des nombres. Cette stratégie dispose d’une surcharge de stockage d’index inférieure et inclut l’indexation géospatiale.
	- **Plage**. Cette stratégie est préférable lorsque vous exécutez des requêtes ORDER BY, de plage et d’égalité sur des nombres et des chaînes. Cette stratégie dispose d’une surcharge de stockage d’index supérieure à **celle par défaut** et inclut l’indexation géospatiale.

	Pour plus d’informations sur les stratégies d’indexation, consultez la rubrique [Stratégies d’indexation DocumentDB](documentdb-indexing-policies.md).

8. Cliquez sur **OK** en bas de l'écran pour créer la nouvelle collection.


9. La nouvelle collection s'affiche maintenant dans le filtre **Collections** dans le **Base de données**.
 
	![Capture d’écran montrant la nouvelle collection dans le panneau Base de données. Portail Azure pour DocumentDB - Créateur de bases de données basées sur le cloud pour les bases de données JSON NoSQL](./media/documentdb-create-collection/docdb-collection-creation-8.png)

## Qu’est-ce qu’une collection DocumentDB ? 

Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript. Une collection est une entité facturable, où le [coût](documentdb-performance-levels.md) est déterminé par le niveau de performances associé à la collection.

Les collections correspondent à la limite de transaction pour les procédures stockées et les déclencheurs, et le point d’entrée pour les requêtes et les opérations CRUD. Chaque collection a un volume réservé de débit qui n’est pas partagé avec les autres collections du même compte. Par conséquent, vous pouvez faire évoluer votre application en termes de stockage et de débit en ajoutant des collections, puis répartir vos documents entre ces dernières.

Les collections diffèrent des tables dans les bases de données relationnelles. Les collections n’appliquent pas de schémas. En fait, DocumentDB n’en applique pas non plus, car il s’agit d’une base de données sans schéma. Par conséquent, vous pouvez stocker différents types de documents avec différents schémas dans la même collection. Vous pouvez choisir d’utiliser des collections pour stocker les objets d’un type unique, comme vous le feriez avec des tables. Le meilleur modèle dépend uniquement de la manière dont les données apparaissent ensemble dans les requêtes et les transactions.

## Autres méthodes de création d'une collection DocumentDB

Les collections ne doivent pas nécessairement être créées avec le portail. Vous pouvez également les créer avec les [Kits de développement logiciel (SDK) DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Pour voir un exemple de code C# sur la création d’une collection avec le kit de développement logiciel .NET DocumentDB, consultez le fichier [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/CollectionManagement/Program.cs) du projet CollectionManagement, disponible dans le référentiel [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) sur [GitHub.com](https://github.com).

## Résolution de problèmes

Si **Ajouter une collection** est désactivé dans le portail Azure, cela signifie que votre compte est actuellement désactivé, ce qui se produit habituellement lorsque les crédits d’avantages du mois sont utilisés.

## Étapes suivantes

Maintenant que vous avez une collection, l'étape suivante consiste à ajouter ou importer des documents dans la collection. Lorsqu'il s'agit d'ajouter des documents à une collection, vous avez le choix :

- Vous pouvez [ajouter des documents](documentdb-view-json-document-explorer.md) à l’aide de l’Explorateur de documents, dans le portail.
- Vous pouvez [importer des documents et des données](documentdb-import-data.md) à l'aide de l'outil de migration de données DocumentDB qui vous permet d'importer des fichiers JSON et CSV ainsi que des données depuis SQL Server, MongoDB, le stockage de table Azure et d’autres collections DocumentDB. 
- Vous pouvez également ajouter des documents en vous aidant des [kits de développement logiciel (SDK) DocumentDB](documentdb-sdk-dotnet.md). DocumentDB possède les kits de développement logiciel (SDK) de .NET, Java, Python, Node.js et de l’API JavaScript. Le fichier [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) du projet DocumentManagement, disponible dans le référentiel [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) sur [GitHub.com](https://github.com), montre des opérations CRUD sur des documents en utilisant le kit de développement logiciel .NET DocumentDB.

Une fois que chaque collection contient des documents, vous pouvez utiliser [DocumentDB SQL](documentdb-sql-query.md) pour [exécuter des requêtes](documentdb-sql-query.md#executing-queries) sur vos documents à l’aide de l’[Explorateur de requête](documentdb-query-collections-query-explorer.md) du portail, de l’[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de l’un des [Kits de développement logiciel](documentdb-sdk-dotnet.md).

<!---HONumber=AcomDC_1223_2015-->