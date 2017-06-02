---
title: "Développer une application Node.js Azure Cosmos DB à l’aide de l’API Graph | Microsoft Docs"
description: "Cet article présente un exemple de code Node.js que vous pouvez utiliser pour vous connecter à Azure Cosmos DB, et pour interroger les données."
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
ms.date: 05/21/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 5c996068ff5fbadda6730244c34c0d0d1f8fb447
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017


---
# <a name="azure-cosmos-db-build-a-nodejs-application-using-the-graph-api"></a>Azure Cosmos DB : Développer une application Node.js grâce à l’API Graph

Azure Cosmos DB est un service de base de données multi-modèles mondialement distribué par Microsoft. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer, à l’aide du Portail Azure, un compte Azure Cosmos DB pour l’API Graph (préversion), une base de données, ainsi qu’un graphique. Par la suite, vous allez développer et exécuter une application console en utilisant le pilote OSS [Gremlin Node.js](https://www.npmjs.com/package/gremlin-secure).  

> [!NOTE]
> Le module NPM `gremlin-secure` est une version modifiée du module `gremlin`, avec la prise en charge de SSL et de SASL requise pour se connecter à Azure Cosmos DB. Le code source est disponible sur [GitHub](https://github.com/CosmosDB/gremlin-javascript).
>

## <a name="prerequisites"></a>Composants requis

* Avant de pouvoir exécuter cet exemple, vous devez posséder les composants requis suivants :
    * [Node.js](https://nodejs.org/en/) version v0.10.29 ou ultérieure
    * [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Ajout d’un graphique

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application API Graph à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git, comme git bash, et accédez à un répertoire de travail à l’aide de la commande `cd`.  

2. Exécutez la commande suivante pour cloner l’exemple de référentiel. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Ouvrez le fichier de solution dans Visual Studio. 

## <a name="review-the-code"></a>Examiner le code

Passons rapidement en revue ce qu’il se passe dans l’application. Ouvrez le fichier `app.js` et vous découvrirez que ces lignes de code. 

* Le client Gremlin est créé.

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

Les configurations sont toutes dans `config.js`, que nous allons modifier dans la section suivante.

* Une série d’étapes Gremlin sont exécutées grâce à la méthode `client.execute`.

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Sur le [Portail Azure](http://portal.azure.com/), dans votre compte Azure Cosmos DB, dans le volet de navigation situé à gauche, cliquez sur **Clés**, puis cliquez sur **Clés en lecture-écriture**. Vous utilisez les boutons Copier sur le côté droit de l’écran pour copier l’URI et la clé primaire dans le fichier `app.js` à l’étape suivante.

    ![Afficher et copier une touche d’accès rapide dans le portail Azure, panneau Clés](./media/create-documentdb-dotnet/keys.png)

2. Copiez votre valeur d’URI Gremlin à partir du portail (à l’aide du bouton Copier) et définissez-la comme valeur de la clé `config.endpoint` dans le fichier config.js. Le point de terminaison Gremlin doit être uniquement le nom d’hôte sans protocole/numéro de port comme `mygraphdb.graphs.azure.com` (pas `https://mygraphdb.graphs.azure.com` ou `mygraphdb.graphs.azure.com:433`).

    `config.endpoint = "GRAPHENDPOINT";`

3. Puis, copiez votre valeur de clé primaire à partir du portail et définissez-la comme la valeur de la config.primaryKey dans config.js. Votre application est maintenant à jour avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

    `config.primaryKey = "PRIMARYKEY";`

4. Entrez le nom de la base de données et le nom du graphique (conteneur) pour la valeur de config.database et de config.collection. 

Voici un exemple de ce à quoi votre fichier config.js terminé doit ressembler :

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "mygraphdb.graphs.azure.com";
config.primaryKey = "OjlhK6tjxfSXyKtrmCiM9O6gQQgu5DmgAoauzD1PdPIq1LZJmILTarHvrolyUYOB0whGQ4j21rdAFwoYep7Kkw==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Exécuter l’application console

1. Ouvrez une fenêtre de terminal et `cd` dans un répertoire d’installation pour le fichier package.json inclus dans le projet.  

2. Exécutez `npm install` pour installer les modules npm requis. Cela inclut `gremlin-secure`.

3. Exécutez `node app.js` sur un terminal pour démarrer votre application Node.

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
