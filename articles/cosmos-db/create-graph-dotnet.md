---
title: "Créer une application .NET Azure Cosmos DB à l’aide de l’API Graph | Microsoft Docs"
description: "Cet article présente un exemple de code .NET que vous pouvez utiliser pour vous connecter à Azure Cosmos DB et pour interroger ce service."
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/21/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 597b12b98edb07e453fc3cd5a0884c584a5bfd14
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-graph-api"></a>Azure Cosmos DB : Créer une application .NET à l’aide de l’API Graph

Azure Cosmos DB est le service de base de données multi-modèle mondialement distribué de Microsoft. Vous pouvez rapidement créer et interroger des bases de données de documents, de paires clé-valeur et de graphiques, qui bénéficient toutes des fonctionnalités de distribution mondiale et de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer un compte, une base de données et un graphique (conteneur) Azure Cosmos DB à l’aide du portail Azure. Vous créerez et exécuterez ensuite une application console basée sur l’[API Graph](graph-sdk-dotnet.md) (préversion).  

## <a name="prerequisites"></a>Composants requis

Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [cosmosdb-create-dbaccount-graph](../../includes/cosmosdb-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Ajout d’un graphique

[!INCLUDE [cosmosdb-create-graph](../../includes/cosmosdb-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application API Graph à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git, comme git bash, et accédez à un répertoire de travail à l’aide de la commande `cd`.  

2. Exécutez la commande suivante pour cloner l’exemple de référentiel. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started.git
    ```

3. Ouvrez le fichier de solution dans Visual Studio. 

## <a name="review-the-code"></a>Examiner le code

Passons rapidement en revue ce qui se passe dans l’application. Ouvrez le fichier Program.cs ; vous pouvez constater que ces lignes de code créent les ressources Azure Cosmos DB. 

* Le DocumentClient est initialisé. Dans la préversion, nous avons ajouté une API d’extension graphique sur le client DocumentDB. Nous travaillons actuellement à la mise au point d’un client graphique autonome dissocié du client et des ressources DocumentDB.

    ```csharp
    using (DocumentClient client = new DocumentClient(
        new Uri(endpoint),
        authKey,
        new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    ```

* Une nouvelle base de données est créée.

    ```csharp
    Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" });
    ```

* Un nouveau graphique est créé.

    ```csharp
    DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri("graphdb"),
        new DocumentCollection { Id = "graph" },
        new RequestOptions { OfferThroughput = 1000 });
    ```
* Une série d’étapes Gremlin sont exécutées à l’aide de la méthode `CreateGremlinQuery`.

    ```csharp
    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, "g.V().count()");
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    ```

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Dans le [portail Azure](http://portal.azure.com/), dans votre compte Azure Cosmos DB, dans le volet de navigation de gauche, cliquez sur **Clés**, puis sur **Clés en lecture-écriture**. Vous utiliserez les boutons Copier sur le côté droit de l’écran pour copier l’URI et la clé primaire dans le fichier `App.config` à l’étape suivante.

    ![Affichage et copie d’une touche d’accès rapide dans le portail Azure, panneau Clés](./media/create-graph-dotnet/keys.png)

2. Dans Visual Studio 2017, ouvrez le fichier `App.config`. 

3. Copiez la valeur de votre URI dans le portail (à l’aide du bouton Copier) et définissez-la comme valeur de la clé du point de terminaison (Endpoint) dans `App.config`. 

    `<add key="Endpoint" value="FILLME.documents.azure.com:443" />`

4. Copiez la valeur de votre clé primaire dans le portail et définissez-la comme valeur de la clé d’authentification (AuthKey) dans `App.config`. 

    `<add key="AuthKey" value="FILLME" />`

Votre application est maintenant à jour avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Exécution de l’application console

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **GraphGetStarted** dans l’**Explorateur de solutions**, puis cliquez sur **Gérer les packages NuGet**. 

2. Dans la zone **Parcourir** de NuGet, tapez *Microsoft.Azure.Graphs* et cochez la case **Inclure les préversions**. 

3. À partir des résultats, installez la bibliothèque **Microsoft.Azure.Graphs**. Cette opération installe le package de bibliothèque d’extension graphique Azure Cosmos DB et toutes les dépendances.

4. Appuyez sur Ctrl + F5 pour exécuter l’application.

   La fenêtre de console affiche les sommets et les bords ajoutés au graphique. Lorsque le script se termine, appuyez deux fois sur ENTRÉE pour fermer la fenêtre de console. 

## <a name="browse-using-the-data-explorer"></a>Navigation à l’aide de l’Explorateur de données

Vous pouvez maintenant retourner à l’Explorateur de données dans le Portail Azure pour parcourir et interroger vos nouvelles données graphiques.

* Dans l’Explorateur de données, la nouvelle base de données apparaît dans le volet Collections. Développez **graphdb**, **graphcoll**, puis cliquez sur **Graph**.

    Les données générées par l’exemple d’application s’affichent dans le volet Graphiques.

## <a name="review-slas-in-the-azure-portal"></a>Examiner les SLA dans le Portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées durant ce guide de démarrage rapide dans le Portail Azure en procédant de la façon suivante : 

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos D, à créer un graphique à l’aide de l’Explorateur de données et à exécuter une application. Vous pouvez maintenant générer des requêtes plus complexes et implémenter une logique de traversée de graphique puissante, à l’aide de Gremlin. 

> [!div class="nextstepaction"]
> [Interroger à l’aide de Gremlin](tutorial-query-graph.md)


