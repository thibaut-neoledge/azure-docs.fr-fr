---
title: "Créer une application .NET Azure Cosmos à l’aide de l’API Table | Microsoft Docs"
description: "Débutez avec les API Tables d’Azure Cosmos DB à l’aide de .NET"
services: cosmosdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 73b9448153ec520d77afd1bdb65b9694e7bf7b9e
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB : Créer une application .NET à l’aide de l’API Table

Azure Cosmos DB est un service de base de données multi-modèles mondialement distribué par Microsoft. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer, à l’aide du Portail Azure, un compte Azure Cosmos DB, et une table dans ce compte. Vous allez ensuite écrire un code pour insérer, mettre à jour, supprimer des entités, et exécuter quelques requêtes. Vous pouvez télécharger le [Kit de développement logiciel (SDK) de Stockage Azure](https://aka.ms/premiumtablenuget) à partir de NuGet. Il présente les mêmes classes et signatures de méthode que le [Kit de développement logiciel (SDK) de Stockage Azure](https://www.nuget.org/packages/WindowsAzure.Storage), mais il peut également se connecter à des comptes Azure Cosmos DB à l’aide de [l’API Table](table-introduction.md) (préversion). 

## <a name="prerequisites"></a>Composants requis

Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmosdb-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Ajouter une table

[!INCLUDE [cosmosdb-create-table](../../includes/cosmosdb-create-table.md)]

## <a name="add-sample-data"></a>Ajouter un exemple de données

Vous pouvez maintenant ajouter des données à votre nouvelle table grâce à l’Explorateur de données.

1. Dans l’Explorateur de données, développez **exemple de base de données**, **exemple de table**, cliquez sur **Entités**, puis cliquez sur **Ajouter une entité**.
2. À présent, ajoutez des données à la zone de valeur PartitionKey et à la zone de valeur RowKey, puis cliquez sur **Ajouter une entité**.

   ![Créer des documents dans l’Explorateur de données, dans le Portail Azure](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
    Vous pouvez maintenant ajouter des entités supplémentaires à votre table, éditer vos entités, ou interroger vos données dans l’Explorateur de données. À partir de l’Explorateur de données, vous pouvez également faire évoluer votre débit et ajouter des procédures stockées, des fonctions définies par l’utilisateur, et ajouter à votre table des déclencheurs.

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Nous allons maintenant cloner une application API DocumentDB à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git, comme git bash, et `cd` vers un répertoire de travail.  

2. Exécutez les commandes suivantes afin de cloner l’exemple de référentiel. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Ensuite, ouvrez le fichier de solution dans Visual Studio. 

## <a name="review-the-code"></a>Examiner le code

Passons rapidement en revue ce qu’il se passe dans l’application. Ouvrez le fichier DocumentDBRepository.cs, et vous découvrirez que ces lignes de code créent les ressources Azure Cosmos DB. 

* Le DocumentClient est initialisé.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* Une nouvelle base de données est créée.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* Un nouveau conteneur graphique est créé.

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```

## <a name="update-your-connection-string"></a>Mettre à jour votre chaîne de connexion

Maintenant, retournez sur le Portail Azure afin d’obtenir vos informations de chaîne de connexion, et copiez-les dans l’application.

1. Sur le [Portail Azure](http://portal.azure.com/), dans votre compte Azure Cosmos DB, dans le volet de navigation situé à gauche, cliquez sur **Clés**, puis cliquez sur **Clés en lecture-écriture**. Vous utiliserez les boutons Copier sur le côté droit de l’écran pour copier l’URI et la clé primaire dans le fichier app.config à l’étape suivante.

    ![Affichage et copie d’une clé d’accès rapide dans le portail Azure, panneau Clés](./media/create-documentdb-dotnet-core/keys.png)

2. Dans Visual Studio, ouvrez le fichier app.config. 

3. Copiez votre nom de compte Azure Cosmos DB à partir du portail, et définissez-le comme la valeur du nom de compte dans la valeur de chaîne PremiumStorageConnection, dans app.config. Dans la capture d’écran ci-dessus, le nom du compte est cosmos-db-démarrage rapide. Votre nom de compte s’affiche en haut du portail.

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMODB.documents.azure.com" />`

4. Ensuite, copiez votre valeur de clé primaire à partir du portail et définissez-la comme la valeur de la propriété AccountKey dans PremiumStorageConnectionString. 

    `AccountKey=AUTHKEY`

5. Enfin, copiez votre valeur URI à partir de la page Clés du portail (à l’aide du bouton Copier) et définissez-la comme la valeur de la propriété TableEndpoint du PremiumStorageConnectionString.

    `TableEndpoint=https://COSMODB.documents.azure.com`

    Vous pouvez laisser le StandardStorageConnectionString en l’état.

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

## <a name="run-the-web-app"></a>Exécuter l’application web

1. Dans Visual Studio, cliquez avec le bouton droit sur le nom du projet dans **Explorateur de solutions**, puis cliquez sur **Gérer les packages NuGet**. 

2. Dans la zone **Parcourir** de NuGet, tapez *WindowsAzure.Storage* et vérifier la zone **Inclure les préversions**. 

3. À partir des résultats, installez la bibliothèque **WindowsAzure.Storage**. Cette opération permet d’installer la préversion du package API Table d’Azure Cosmos DB, ainsi que toutes les dépendances.

4. Appuyez sur Ctrl + F5 pour exécuter l'application.

    La fenêtre de console affiche les données ajoutées à la table. Lorsque le script se termine, fermez la fenêtre de console. 

Vous pouvez dès à présent revenir à l’Explorateur de données et voir la requête, modifier et travailler avec ces nouvelles données. 

## <a name="review-slas-in-the-azure-portal"></a>Examiner les SLA dans le Portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées durant ce guide de démarrage rapide dans le Portail Azure en procédant de la façon suivante : 

1. À partir du menu de gauche dans le Portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer une table à l’aide de l’Explorateur de données, et à exécuter une application.  Maintenant, vous pouvez interroger vos données à l’aide de l’API Table.  

> [!div class="nextstepaction"]
> [Requête à l’aide de l’API Table](tutorial-query-table.md)


