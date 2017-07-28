---
title: "Développer une application Java Azure Cosmos DB à l’aide de l’API Graph | Microsoft Docs"
description: "Cet article présente un exemple de code Java que vous pouvez utiliser pour vous connecter aux données graphiques et pour les interroger dans Azure Cosmos DB à l’aide de Gremlin."
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 8eac406c6ef96d7ae8dd5f4931c7d16edb723be8
ms.contentlocale: fr-fr
ms.lasthandoff: 07/17/2017

---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>Azure Cosmos DB : Créer une application Java à l’aide de l’API Graph

Azure Cosmos DB est un service de base de données multi-modèles mondialement distribué par Microsoft. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer, à l’aide du Portail Azure, un compte Azure Cosmos DB pour l’API Graph (préversion), une base de données, ainsi qu’un graphique. Ensuite, vous allez créer et exécuter une application console en utilisant le pilote OSS [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver).  

## <a name="prerequisites"></a>Composants requis

* Avant de pouvoir exécuter cet exemple, vous devez posséder les composants requis suivants :
   * JDK 1.7 + (exécutez `apt-get install default-jdk` si vous n’avez pas JDK), et définissez des variables d’environnement telles que `JAVA_HOME`
   * Maven (exécutez `apt-get install maven` si vous ne possédez pas Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Ajout d’un graphique

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application API Graph (préversion) à partir de GitHub, configurer la chaîne de connexion, et l’exécuter. Vous pouvez constater à quel point il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git, comme git bash, et accédez à un répertoire de travail à l’aide de la commande `cd`.  

2. Exécutez la commande suivante pour cloner l’exemple de référentiel : 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Examiner le code

Passons rapidement en revue ce qu’il se passe dans l’application. Ouvrez le fichier `Program.java` et vous découvrirez que ces lignes de code. 

* Le Gremlin `Client` est initialisé à partir de la configuration dans `src/remote.yaml`.

    ```java
    Cluster cluster = Cluster.build(new File("src/remote.yaml")).create();
    
    Client client = cluster.connect();
    ```

* Une série d’étapes Gremlin sont exécutées à l’aide de la méthode `client.submit`.

    ```java
    ResultSet results = client.submit("g.V()");

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```
## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

1. Ouvrez le fichier src/remote.yaml. 

3. Renseignez les configurations *hôte*, *port*, *nom d’utilisateur*, *mot de passe*, *connectionPool* et *sérialiseur* dans le fichier src/remote.yaml :

    Paramètre|Valeur suggérée|Description
    ---|---|---
    Hôtes|[***.graphs.azure.com]|Reportez-vous à la capture d’écran ci-dessous. Il s’agit de la valeur URI Gremlin sur la page Vue d’ensemble du portail Azure, entre crochets, avec la fin : 443/ supprimée.<br><br>Cette valeur peut également être récupérée à partir de l’onglet Clés, à l’aide de la valeur URI, en supprimant https://, en changeant les documents en graphiques et en supprimant la fin : 443/.
    Port|443|Définissez la valeur sur 443.
    Nom d’utilisateur|*Votre nom d’utilisateur*|Ressource sous la forme `/dbs/<db>/colls/<coll>`, où `<db>` est le nom de votre base de données et `<coll>` le nom de votre collection.
    Mot de passe|*Votre clé principale primaire*|Reportez-vous à la deuxième capture d’écran ci-dessous. Il s’agit de votre clé primaire, que vous pouvez récupérer à partir de la page Clés du portail Azure, dans la zone Clé primaire. Utilisez le bouton Copier sur le côté gauche de la zone pour copier la valeur.
    ConnectionPool|{enableSsl: true}|Votre paramètre de pool de connexions pour SSL.
    serializer|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: {serializeResultToString: true }}|Définissez le paramètre sur cette valeur et supprimez tous les sauts de ligne `\n` quand vous collez la valeur.

    Pour la valeur Hôtes, copiez la valeur **URI Gremlin** à partir de la page **Vue d’ensemble** : ![Afficher et copier la valeur URI Gremlin sur la page Vue d’ensemble dans le portail Azure](./media/create-graph-java/gremlin-uri.png)

    Pour la valeur Mot de passe, copiez la **clé primaire** dans la page **Clés** : ![Afficher et copier votre clé primaire dans le portail Azure, la page Clés](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>Exécution de l’application console

1. Exécutez `mvn package` sur un terminal pour installer les packages Java requis.

2. Exécutez `mvn exec:java -D exec.mainClass=GetStarted.Program` sur un terminal pour démarrer votre application Java.

Vous pouvez dès à présent revenir à l’Explorateur de données et voir la requête, modifier et travailler avec ces nouvelles données. 

## <a name="browse-using-the-data-explorer"></a>Parcourir à l’aide de l’Explorateur de données

Vous pouvez maintenant retourner à l’Explorateur de données dans le Portail Azure pour parcourir et interroger vos nouvelles données graphiques.

* Dans l’Explorateur de données, la nouvelle base de données apparaît dans le volet Collections. Développez **graphdb**, **graphcoll**, puis cliquez sur **Graph**.

    Les données générées par l’exemple d’application s’affichent dans le volet Graphiques.

## <a name="review-slas-in-the-azure-portal"></a>Examiner les SLA dans le Portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées durant ce guide de démarrage rapide dans le Portail Azure en procédant de la façon suivante : 

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos D, à créer un graphique à l’aide de l’Explorateur de données et à exécuter une application. Vous pouvez maintenant générer des requêtes plus complexes et implémenter une logique de traversée de graphique puissante, à l’aide de Gremlin. 

> [!div class="nextstepaction"]
> [Interroger à l’aide de Gremlin](tutorial-query-graph.md)


