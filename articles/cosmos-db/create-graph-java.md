---
title: "Développer une application Java Azure Cosmos DB à l’aide de l’API Graph | Microsoft Docs"
description: "Cet article présente un exemple de code Java que vous pouvez utiliser pour vous connecter aux données graphiques et pour les interroger dans Azure Cosmos DB à l’aide de Gremlin."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/27/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: d9619bd9a012a347634282788b3a318886967a3f
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017


---
<a id="azure-cosmos-db-build-a-java-application-using-the-graph-api" class="xliff"></a>

# Azure Cosmos DB : Créer une application Java à l’aide de l’API Graph

Azure Cosmos DB est un service de base de données multi-modèles mondialement distribué par Microsoft. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer, à l’aide du Portail Azure, un compte Azure Cosmos DB pour l’API Graph (préversion), une base de données, ainsi qu’un graphique. Ensuite, vous allez créer et exécuter une application console en utilisant le pilote OSS [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver).  

<a id="prerequisites" class="xliff"></a>

## Composants requis

* Avant de pouvoir exécuter cet exemple, vous devez posséder les composants requis suivants :
   * JDK 1.7 + (exécutez `apt-get install default-jdk` si vous n’avez pas JDK), et définissez des variables d’environnement telles que `JAVA_HOME`
   * Maven (exécutez `apt-get install maven` si vous ne possédez pas Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-a-database-account" class="xliff"></a>

## Création d'un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

<a id="add-a-graph" class="xliff"></a>

## Ajout d’un graphique

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

<a id="clone-the-sample-application" class="xliff"></a>

## Clonage de l’exemple d’application

À présent, nous allons cloner une application API Graph (préversion) à partir de GitHub, configurer la chaîne de connexion, et l’exécuter. Vous pouvez constater à quel point il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git, comme git bash, et accédez à un répertoire de travail à l’aide de la commande `cd`.  

2. Exécutez la commande suivante pour cloner l’exemple de référentiel : 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

<a id="review-the-code" class="xliff"></a>

## Examiner le code

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
<a id="update-your-connection-string" class="xliff"></a>

## Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Sur le [Portail Azure](http://portal.azure.com/), dans votre compte Azure Cosmos DB, dans le volet de navigation situé à gauche, cliquez sur **Clés**, puis cliquez sur **Clés en lecture-écriture**. Vous utilisez les boutons Copier sur le côté droit de l’écran pour copier l’URI et la clé primaire dans le fichier `Program.java` à l’étape suivante.

    ![Affichage et copie d’une touche d’accès rapide dans le portail Azure, panneau Clés](./media/create-graph-java/keys.png)

2. Ouvrez le fichier `src/remote.yaml` . 

3. Renseignez vos configurations *hôte*, *port*, *nom d’utilisateur*, *mot de passe*, *connectionPool*, et *sérialiseur* dans le fichier `src/remote.yaml`  :

    Paramètre|Valeur suggérée|Description
    ---|---|---
    Hôtes|***. graphs.azure.com|Votre service graphique URI, que vous pouvez récupérer à partir du portail Azure
    Port|443|Définir à 443
    Nom d’utilisateur|*Votre nom d’utilisateur*|La ressource au format `/dbs/<db>/colls/<coll>`.
    Mot de passe|*Votre clé principale primaire*|Votre clé principale primaire pour Azure Cosmos DB
    ConnectionPool|{enableSsl: true}|Votre paramètre de pool de connexions pour SSL
    serializer|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: {serializeResultToString: true }}|Définir à cette valeur

<a id="run-the-console-app" class="xliff"></a>

## Exécution de l’application console

1. Exécutez `mvn package` sur un terminal pour installer les packages Java requis.

2. Exécutez `mvn exec:java -D exec.mainClass=GetStarted.Program` sur un terminal pour démarrer votre application Java.

Vous pouvez dès à présent revenir à l’Explorateur de données et voir la requête, modifier et travailler avec ces nouvelles données. 

<a id="browse-using-the-data-explorer" class="xliff"></a>

## Parcourir à l’aide de l’Explorateur de données

Vous pouvez maintenant retourner à l’Explorateur de données dans le Portail Azure pour parcourir et interroger vos nouvelles données graphiques.

* Dans l’Explorateur de données, la nouvelle base de données apparaît dans le volet Collections. Développez **graphdb**, **graphcoll**, puis cliquez sur **Graph**.

    Les données générées par l’exemple d’application s’affichent dans le volet Graphiques.

<a id="review-slas-in-the-azure-portal" class="xliff"></a>

## Examiner les SLA dans le Portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

<a id="clean-up-resources" class="xliff"></a>

## Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées durant ce guide de démarrage rapide dans le Portail Azure en procédant de la façon suivante : 

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos D, à créer un graphique à l’aide de l’Explorateur de données et à exécuter une application. Vous pouvez maintenant générer des requêtes plus complexes et implémenter une logique de traversée de graphique puissante, à l’aide de Gremlin. 

> [!div class="nextstepaction"]
> [Interroger à l’aide de Gremlin](tutorial-query-graph.md)


