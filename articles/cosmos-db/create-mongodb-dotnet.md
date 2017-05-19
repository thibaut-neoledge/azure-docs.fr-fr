---
title: "Azure Cosmos DB : Développer une application web avec .NET et l’API MongoDB | Microsoft Docs"
description: "Cet article présente un exemple de code .NET que vous pouvez utiliser pour vous connecter à l’API MongoDB d’Azure Cosmos DB, et pour l’interroger"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 68dccb429a9214e62daad9c161ffb8ad2a7b9a0b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-mongodb-api-web-app-with-net-and-the-azure-portal"></a>Azure Cosmos DB : Développer une application web API MongoDB avec .NET et le Portail Azure

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous pouvez rapidement créer et interroger des documents, des paires clé/valeur et des bases de données de graphiques, tous profitant de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer, à l’aide du Portail Azure, un compte Azure Cosmos DB , une base de données de documents, ainsi qu’une collection. Vous allez ensuite créer et déployer une application web de liste des tâches basée sur le [pilote .NET MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/). 

## <a name="prerequisites"></a>Composants requis

Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [documentdb-create-dbaccount](../../includes/cosmosdb-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application API MongoDB à partir de GitHub, configurer la chaîne de connexion, et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git, comme git bash, et accédez à un répertoire de travail à l’aide de la commande `cd`.  

2. Exécutez la commande suivante pour cloner l’exemple de référentiel : 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

3. Ouvrez le fichier de solution dans Visual Studio. 

## <a name="review-the-code"></a>Examen du code

Passons rapidement en revue ce qui se passe dans l’application. Ouvrez le fichier **Dal.cs** dans le répertoire **DAL**, et vous découvrirez que ces lignes de code créent les ressources Azure Cosmos DB. 

* Initialisez le client Mongo.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credentials = new List<MongoCredential>()
        {
            new MongoCredential("SCRAM-SHA-1", identity, evidence)
        };

        MongoClient client = new MongoClient(settings);
    ```

* Récupérez la base de données et la collection.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Récupérez tous les documents.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Dans le [portail Azure](http://portal.azure.com/), dans votre compte Azure Cosmos DB, dans le volet de navigation de gauche, cliquez sur **Chaîne de connexion**, puis sur **Clés en lecture-écriture**. Vous utiliserez le bouton Copier sur le côté droit de l’écran pour copier le nom d’utilisateur, le mot de passe et l’hôte dans le fichier Dal.cs à l’étape suivante.

2. Ouvrez le fichier **Dal.cs** dans le répertoire **DAL**. 

3. Copiez votre valeur **nom d’utilisateur** à partir du portail (à l’aide du bouton Copier) et définissez-la comme valeur de **nom d’utilisateur** dans le fichier **Dal.cs**. 

4. Puis, copiez votre valeur **hôte** à partir du portail et définissez-la comme valeur **hôte** dans votre fichier **Dal.cs**. 

5. Puis, copiez votre valeur **mot de passe** à partir du portail et définissez-la comme valeur **mot de passe** dans votre fichier **Dal.cs**. 

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 
    
## <a name="run-the-web-app"></a>Exécution de l’application web

1. Dans Visual Studio, cliquez avec le bouton droit sur le nom du projet dans l’**Explorateur de solutions**, puis cliquez sur **Gérer les packages NuGet**. 

2. Dans la zone **Parcourir**de NuGet, tapez *MongoDB.Driver*.

3. À partir des résultats, installez la bibliothèque **MongoDB.Driver**. Cette opération permet d’installer le package MondoDB.Driver ainsi que toutes les dépendances.

4. Appuyez sur Ctrl + F5 pour exécuter l’application. Votre application s’affiche dans votre navigateur. 

5. Cliquez sur **Créer** dans le navigateur et créez quelques tâches dans votre application de liste des tâches.

## <a name="review-slas-in-the-azure-portal"></a>Examen des SLA dans le Portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées durant ce guide de démarrage rapide dans le Portail Azure; en procédant de la façon suivante :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB et à exécuter une application web à l’aide de l’API de MongoDB. Vous pouvez maintenant importer des données supplémentaires dans votre compte Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB pour l’API MongoDB](../documentdb/documentdb-mongodb-migrate.md)


