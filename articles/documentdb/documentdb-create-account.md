<properties 
	pageTitle="Création d’un compte de base de données - version d’évaluation gratuite | Microsoft Azure" 
	description="Apprenez à créer des comptes de base de données avec le portail du service en ligne pour Azure DocumentDB, un service de gestion de base de données de documents NoSQL géré pour JSON. Essayez gratuitement dès aujourd’hui."
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="06/26/2015" 
	ms.author="mimig"/>

# Création d’un compte de base de données DocumentDB avec le portail Azure en version préliminaire

Pour utiliser Microsoft Azure DocumentDB, vous devez créer un compte de base de données DocumentDB à l’aide du portail Azure en version préliminaire.

Vous débutez avec DocumentDB ? Regardez [cette vidéo](http://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) de quatre minutes réalisée par Scott Hanselman pour voir comment effectuer les tâches les plus courantes dans le portail en ligne.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Étapes suivantes

Maintenant que vous disposez d’un compte DocumentDB, l’étape suivante consiste à créer une base de données DocumentDB. Pour créer une base de données, utilisez au choix :

- les exemples C# .NET du projet [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) dans le référentiel [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) sur GitHub ;
- le portail en version préliminaire, tel que décrit dans la rubrique [Création d’une base de données DocumentDB avec le portail Azure en version préliminaire](documentdb-create-database.md) ;
- les didacticiels complets : [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) ou [Python](documentdb-python-application.md) ;
- les [kits de développement logiciel (SDK) de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB possède les kits de développement logiciel (SDK) de .NET, Java, Python, Node.js et de l’API JavaScript. 


Après avoir créé la base de données, vous devez lui [ajouter une ou plusieurs collections](documentdb-create-collection.md), puis [ajouter des documents](documentdb-view-json-document-explorer.md) aux collections.

Une fois que chaque collection contient des documents, vous pouvez utiliser [DocumentDB SQL](documentdb-sql-query.md) pour [exécuter des requêtes](documentdb-sql-query.md#executing-queries) sur vos documents à l’aide de l’[Explorateur de requête](documentdb-query-collections-query-explorer.md) dans la version préliminaire du portail, de l’[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de l’un des [kits de développement logiciel](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Pour en savoir plus sur DocumentDB, explorez les ressources suivantes :

-	[Carte d’apprentissage de DocumentDB](documentdb-learning-map.md)
-	[Modèle de ressource et concepts de DocumentDB](documentdb-resources.md)

 

<!---HONumber=July15_HO4-->