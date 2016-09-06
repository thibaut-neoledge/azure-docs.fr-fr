<properties
	pageTitle="Création d’un compte DocumentDB | Microsoft Azure"
	description="Créer une base de données NoSQL avec Microsoft Azure DocumentDB. Suivez ces instructions pour créer un compte DocumentDB, puis pour créer votre base de données NoSQL ultrarapide et de portée internationale." 
	keywords="créer une base de données"
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
	ms.date="08/25/2016"
	ms.author="mimig"/>

# Création d’un compte DocumentDB à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](documentdb-create-account.md)
- [Interface de ligne de commande Azure et ARM](documentdb-automation-resource-manager-cli.md)

Pour créer une base de données avec Microsoft Azure DocumentDB, vous devez :

- Avoir un compte Azure. Si vous n’en avez pas, vous pouvez obtenir un [compte Azure gratuit](https://azure.microsoft.com/free)
- Création d’un compte DocumentDB.

Vous pouvez créer un compte DocumentDB avec le portail Azure, à l’aide de modèles Azure Resource Manager ou de l’interface de ligne de commande Azure (CLI). Cet article décrit la procédure de création d’un compte DocumentDB à l’aide du portail Azure. Pour créer un compte à l’aide d’Azure Resource Manager et de l’interface de ligne de commande Azure, consultez l’article [Automatisation de la création de compte de base de données DocumentDB](documentdb-automation-resource-manager-cli.md).

Vous débutez avec DocumentDB ? Regardez [cette vidéo](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) de quatre minutes réalisée par Scott Hanselman pour voir comment effectuer les tâches les plus courantes dans le portail en ligne.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Étapes suivantes

Maintenant que vous disposez d’un compte DocumentDB, l’étape suivante consiste à créer une base de données DocumentDB. Pour créer une base de données, utilisez au choix :

- le portail Azure, tel que décrit dans la rubrique [Création d’une base de données DocumentDB avec le portail Azure](documentdb-create-database.md) ;
- les exemples C# .NET du projet [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) dans le référentiel [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) sur GitHub ;
- les didacticiels complets : [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) ou [Python](documentdb-python-application.md) ;
- les [kits de développement logiciel (SDK) de DocumentDB](documentdb-sdk-dotnet.md). DocumentDB possède les kits de développement logiciel (SDK) de .NET, Java, Python, Node.js et de l’API JavaScript.


Après avoir créé la base de données, vous devez lui [ajouter une ou plusieurs collections](documentdb-create-collection.md), puis [ajouter des documents](documentdb-view-json-document-explorer.md) aux collections.

Une fois que chaque collection contient des documents, vous pouvez utiliser [DocumentDB SQL](documentdb-sql-query.md) pour [exécuter des requêtes](documentdb-sql-query.md#executing-queries) sur vos documents à l’aide de l’[Explorateur de requête](documentdb-query-collections-query-explorer.md) du portail, de l’[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de l’un des [Kits de développement logiciel](documentdb-sdk-dotnet.md).

Pour en savoir plus sur DocumentDB, explorez les ressources suivantes :

-	[Parcours d’apprentissage de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Modèle de ressource et concepts de DocumentDB](documentdb-resources.md)

<!---HONumber=AcomDC_0831_2016-->