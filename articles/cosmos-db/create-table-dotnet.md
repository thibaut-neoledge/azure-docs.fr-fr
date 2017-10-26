---
title: "Créer une application .NET Azure Cosmos à l’aide de l’API Table | Microsoft Docs"
description: "Débutez avec les API Tables d’Azure Cosmos DB à l’aide de .NET"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/22/2017
ms.author: arramac
ms.openlocfilehash: 0ce99a4754d7ec6f35bda63af6fc0166cf7e0eb4
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB : Créer une application .NET à l’aide de l’API Table

Azure Cosmos DB est un service de base de données multi-modèles mondialement distribué par Microsoft. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer, à l’aide du Portail Azure, un compte Azure Cosmos DB, et une table dans ce compte. Vous allez ensuite écrire du code pour insérer, mettre à jour et supprimer des entités, et exécuter certaines requêtes à l’aide du nouveau package [Windows Azure Storage Premium Table](https://aka.ms/premiumtablenuget) (préversion) de NuGet. Cette bibliothèque présente les mêmes classes et signatures de méthode que le [Kit de développement logiciel (SDK) du stockage Windows Azure](https://www.nuget.org/packages/WindowsAzure.Storage), mais il peut également se connecter à des comptes Azure Cosmos DB à l’aide de Windows Azure Storage [l’API Table](table-introduction.md) (préversion). 

## <a name="prerequisites"></a>Composants requis

Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Ajouter une table

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Ajouter un exemple de données

Vous pouvez maintenant ajouter des données à votre nouvelle table grâce à l’Explorateur de données (préversion).

1. Dans l’Explorateur de données, développez **exemple de table**, cliquez sur **Entités**, puis cliquez sur **Ajouter une entité**.

   ![Créer des entités dans l’Explorateur de données, dans le portail Azure](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. À présent, ajoutez des données à la zone de valeur PartitionKey et à la zone de valeur RowKey, puis cliquez sur **Ajouter une entité**.

   ![Définir la clé de Partition et la clé de ligne pour une nouvelle entité](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Vous pouvez maintenant ajouter des entités supplémentaires à votre table, éditer vos entités, ou interroger vos données dans l’Explorateur de données. À partir de l’Explorateur de données, vous pouvez également faire évoluer votre débit et ajouter des procédures stockées, des fonctions définies par l’utilisateur, et ajouter à votre table des déclencheurs.

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application Table à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git, comme git bash, et accédez à un répertoire de travail à l’aide de la commande `cd`.  

2. Exécutez la commande suivante pour cloner l’exemple de référentiel. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Ouvrez le fichier de solution dans Visual Studio. 

## <a name="review-the-code"></a>Examiner le code

Passons rapidement en revue ce qui se passe dans l’application. Ouvrez le fichier Program.cs ; vous pouvez constater que ces lignes de code créent les ressources Azure Cosmos DB. 

* L’élément CloudTableClient est initialisé.

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* Une nouvelle table est créée si elle n’existe pas.

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* Une série d’étapes sont exécutées sur la table à l’aide de la classe `TableOperation`.

   ```csharp
   TableOperation insertOperation = TableOperation.Insert(item);
   table.Execute(insertOperation);
   ```
   
   ```csharp
   TableOperation retrieveOperation = TableOperation.Retrieve<T>(items[i].PartitionKey, items[i].RowKey);
   table.Execute(retrieveOperation);
   ```
   
   ```csharp
   TableOperation deleteOperation = TableOperation.Delete(items[i]);
   table.Execute(deleteOperation);
   ```


## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Nous allons maintenant mettre à jour les informations de la chaîne de connexion pour que votre application puisse communiquer avec Azure Cosmos DB. 

1. Dans Visual Studio, ouvrez le fichier app.config. 

2. Dans le [portail Azure](http://portal.azure.com/), dans le menu de navigation gauche d’Azure Cosmos DB, cliquez sur la **chaîne de connexion**. Puis, cliquez sur le bouton Copier pour la chaîne de connexion dans le nouveau volet. 

    ![Afficher et copier le point de terminaison et la clé de compte dans le volet de la chaîne de connexion](./media/create-table-dotnet/keys.png)

3. Collez la valeur dans le fichier app.config en tant que valeur de PremiumStorageConnectionString. 

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`    

    Vous pouvez laisser le StandardStorageConnectionString en l’état.

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Exécuter l’application console

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **PremiumTableGetStarted** dans l’**Explorateur de solutions**, puis cliquez sur **Gérer les packages NuGet**. 

2. Dans la zone **Parcourir** de NuGet, tapez *WindowsAzure.Storage-PremiumTable*.

3. Cochez la case **Inclure les préversions**. 

4. À partir des résultats, installez la bibliothèque **WindowsAzure.Storage-PremiumTable**. Cette opération permet d’installer la préversion du package API Table d’Azure Cosmos DB, ainsi que toutes les dépendances. Notez qu’il s’agit d’un package NuGet différent du package de stockage Windows Azure utilisé par le stockage de Table Azure. 

5. Appuyez sur Ctrl + F5 pour exécuter l’application.

    La fenêtre de console affiche les données ajoutées, récupérées, interrogées, remplacées et supprimées de la table. Lorsque le script se termine, appuyez sur n’importe quelle touche pour fermer la fenêtre de console. 
    
    ![Sortie de la console du démarrage rapide](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-console-output.png)

6. Si vous souhaitez voir les nouvelles entités dans l’Explorateur de données, il suffit de commenter les lignes 188 à 208 dans program.cs afin qu’elles ne soient pas supprimées, puis exécutez de nouveau l’exemple. 

    Vous pouvez maintenant revenir à l’Explorateur de données, cliquer sur **Actualiser**, développer la table **personnes** et cliquer sur **Entités**, puis travailler avec ces nouvelles données. 

    ![Nouvelles entités dans l’Explorateur de données](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Examiner les SLA dans le Portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées durant ce guide de démarrage rapide dans le Portail Azure en procédant de la façon suivante : 

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer une table à l’aide de l’Explorateur de données, et à exécuter une application.  Maintenant, vous pouvez interroger vos données à l’aide de l’API Table.  

> [!div class="nextstepaction"]
> [Requête à l’aide de l’API Table](tutorial-query-table.md)

