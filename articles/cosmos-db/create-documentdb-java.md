---
title: "Créer une base de données de documents Azure Cosmos DB avec Java | Microsoft Docs"
description: "Présente un exemple de code Java que vous pouvez utiliser pour vous connecter à l’API DocumentDB d’Azure Cosmos DB et pour interroger cette dernière."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 08/02/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: df1a25d703a7b8082bdabb4f7d593cb005d416fe
ms.contentlocale: fr-fr
ms.lasthandoff: 08/17/2017

---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB : créer une base de données de documents à l’aide de Java et du portail Azure

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide crée une base de données de documents à l’aide des outils du portail Azure pour Azure Cosmos DB. Ce guide vous indique également comment créer rapidement une application console Java au moyen de [l’API Java DocumentDB](documentdb-sdk-java.md). Les instructions de ce guide de démarrage rapide s’appliquent à tous les systèmes d’exploitation pouvant exécuter Java. Après avoir suivi ce guide de démarrage rapide, vous saurez comment créer et modifier des ressources de base de données de documents dans l’interface utilisateur ou par programme, selon la méthode que vous préférez.

## <a name="prerequisites"></a>Composants requis

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * Sur Ubuntu, exécutez `apt-get install default-jdk` pour installer le JDK.
    * Veillez à définir la variable d’environnement JAVA_HOME pour qu’elle pointe vers le dossier dans lequel le JDK est installé.
* [Téléchargement](http://maven.apache.org/download.cgi) et [installation](http://maven.apache.org/install.html) d’une archive binaire [Maven](http://maven.apache.org/)
    * Sur Ubuntu, vous pouvez exécuter `apt-get install maven` pour installer Maven.
* [Git](https://www.git-scm.com/)
    * Sur Ubuntu, vous pouvez exécuter `sudo apt-get install git` pour installer Git.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

Pour être en mesure de créer une base de données de documents, vous devez avoir préalablement créé un compte de base de données (DocumentDB) SQL avec Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Ajouter une collection

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Ajouter un exemple de données

Vous pouvez maintenant ajouter des données à votre nouvelle collection grâce à l’Explorateur de données.

1. Dans l’Explorateur de données, la nouvelle base de données apparaît dans le volet Collections. Développez la base de données **Tâches**, développez la collection **Éléments**, cliquez sur **Documents**, puis cliquez sur **Nouveaux documents**. 

   ![Créer des documents dans l’Explorateur de données, dans le Portail Azure](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
2. À présent, ajoutez un document à la collection avec la structure suivante.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Une fois le fichier json ajouté à l’onglet **Documents**, cliquez sur **Enregistrer**.

    ![Copiez dans les données json, puis cliquez sur Enregistrer dans l’Explorateur de données du portail Azure](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-save-document.png)

4.  Créez et enregistrez un document supplémentaire dans lequel vous insérez une valeur unique pour la propriété `id` et modifiez les autres propriétés selon vos besoins. Vos nouveaux documents peuvent avoir la structure de votre choix car Azure Cosmos DB n’impose aucun schéma pour vos données.

     Vous pouvez à présent utiliser des requêtes dans l’Explorateur de données pour récupérer vos données en cliquant sur les boutons **Modifier le filtre** et **Appliquer un filtre**. Par défaut, l’Explorateur de données utilise `SELECT * FROM c` pour récupérer tous les documents dans la collection, mais vous pouvez remplacer cette requête par une autre [requête SQL](documentdb-sql-query.md), telle que `SELECT * FROM c ORDER BY c._ts DESC`, pour obtenir tous les documents dans l’ordre décroissant en fonction de leur horodateur. 
 
     Vous pouvez également utiliser l’Explorateur de données pour créer des procédures stockées, des fonctions définies par l'utilisateur et des déclencheurs afin d’exécuter la logique métier côté serveur ainsi que la mise à l’échelle du débit. L’Explorateur de données affiche tous les accès aux données par programmation intégrés disponibles dans l’API, mais permet d’accéder facilement à vos données dans le portail Azure.

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, travaillons sur le code. Nous allons cloner une application API DocumentDB à partir de GitHub, définir la chaîne de connexion et l’exécuter. Vous pouvez constater à quel point il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git, comme git bash, et accédez à un répertoire de travail à l’aide de la commande `CD`.  

2. Exécutez la commande suivante pour cloner l’exemple de référentiel : 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>Examiner le code

Passons rapidement en revue ce qui se passe dans l’application. Ouvrez le fichier `Program.java` du dossier \src\GetStarted et recherchez les lignes de code ci-après qui créent les ressources Azure Cosmos DB. 

* Le `DocumentClient` est initialisé.

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* Une nouvelle base de données est créée.

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* Une nouvelle collection est créée.

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* Certains documents sont créés.

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* Une requête SQL sur JSON est effectuée.

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application. Cette opération permettra à votre application de communiquer avec votre base de données hébergée.

1. Dans le [portail Azure](http://portal.azure.com/), dans votre compte Azure Cosmos DB, dans le volet de navigation de gauche, cliquez sur **Clés**, puis sur **Clés en lecture-écriture**. Vous utiliserez les boutons de copie sur le côté droit de l’écran pour copier l’URI et la CLÉ PRIMAIRE dans le fichier `Program.java` à l’étape suivante.

    ![Affichage et copie d’une clé d’accès rapide dans le portail Azure, panneau Clés](./media/create-documentdb-dotnet/keys.png)

2. Dans le fichier `Program.java`, copiez votre valeur URI à partir du portail (à l’aide du bouton de copie) et définissez-la comme valeur du point de terminaison du constructeur DocumentClient dans `Program.java`. 

    `"https://FILLME.documents.azure.com"`

4. Ensuite, copiez votre valeur CLÉ PRIMAIRE à partir du portail et collez-la à la place de « FILLME » afin de la définir comme second paramètre du constructeur DocumentClient. Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 
    
## <a name="run-the-app"></a>Exécution de l'application

1. Dans la fenêtre de terminal git, exécutez la commande `cd` pour accéder au dossier azure-cosmos-db-documentdb-java-getting-started.

2. Dans la fenêtre de terminal git, tapez `mvn package` pour installer les packages Java requis.

3. Dans la fenêtre de terminal git, exécutez `mvn exec:java -D exec.mainClass=GetStarted.Program` pour démarrer votre application Java.

    Dans la fenêtre de terminal, une notification vous indique que la base de données FamilyDB a été créée et vous invite à appuyer sur une touche pour continuer. Appuyez sur une touche pour créer la base de données, puis basculez vers l’Explorateur de données et constatez qu’il contient désormais une base de données FamilyDB. Continuez à appuyer sur des touches pour créer la collection et les documents, puis exécutez une requête. Une fois le projet terminé, les ressources sont supprimées de votre compte. 

    ![Affichage et copie d’une clé d’accès rapide dans le portail Azure, panneau Clés](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées durant ce guide de démarrage rapide dans le Portail Azure en procédant de la façon suivante :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, une base de données de documents et une collection à l’aide de l’Explorateur de données, puis à exécuter une application pour effectuer la même opération par programme. Vous pouvez maintenant importer des données supplémentaires à votre compte Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB](import-data.md)



