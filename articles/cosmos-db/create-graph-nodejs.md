---
title: "Créer une application Node.js Azure Cosmos DB à l’aide de l’API Graph | Microsoft Docs"
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
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: b9e8c46ba2f029f8dae2b357f05a806d769d0920
ms.contentlocale: fr-fr
ms.lasthandoff: 06/07/2017


---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB : Créer une application Node.js à l’aide de l’API Graph

Azure Cosmos DB est le service de base de données multi-modèle mondialement distribué de Microsoft. Vous pouvez rapidement créer et interroger des bases de données de documents, de paires clé-valeur et de graphiques, qui bénéficient toutes des fonctionnalités de distribution mondiale et de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer, à l’aide du portail Azure, un compte Azure Cosmos DB pour l’API Graph (préversion), une base de données, ainsi qu’un graphique. Par la suite, vous allez créer et exécuter une application console en utilisant le pilote [Gremlin Node.js](https://www.npmjs.com/package/gremlin-secure) open-source.  

> [!NOTE]
> Le module npm `gremlin-secure` est une version modifiée du module `gremlin`, avec la prise en charge de SSL et de SASL requise pour se connecter à Azure Cosmos DB. Le code source est disponible sur [GitHub](https://github.com/CosmosDB/gremlin-javascript).
>

## <a name="prerequisites"></a>Composants requis

Avant de pouvoir exécuter cet exemple, vous devez posséder les composants requis suivants :
* [Node.js](https://nodejs.org/en/) version 0.10.29 ou supérieure
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Ajout d’un graphique

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application API Graph à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git, comme git bash, et indiquez un répertoire de travail (à l’aide de la commande `cd`).  

2. Exécutez la commande suivante pour cloner l’exemple de référentiel. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Ouvrez le fichier de solution dans Visual Studio. 

## <a name="review-the-code"></a>Examiner le code

Passons rapidement en revue ce qui se passe dans l’application. Ouvrez le fichier `app.js`. Les lignes de code suivantes doivent s’afficher. 

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

* Une série d’étapes Gremlin sont exécutées à l’aide de la méthode `client.execute`.

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure, afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Dans le [portail Azure](http://portal.azure.com/), accédez à votre compte Azure Cosmos DB et, dans le menu de navigation de gauche, cliquez sur **Clés**, puis sur **Clés en lecture-écriture**. Vous allez utiliser les boutons Copier figurant sur le côté droit de l’écran pour copier l’URI et la clé primaire dans le fichier `app.js` à l’étape suivante.

    ![Panneau Clés du portail Azure](./media/create-graph-nodejs/keys.png)

2. Copiez votre valeur d’URI Gremlin à partir du portail (à l’aide du bouton Copier) et définissez-la comme valeur de la clé `config.endpoint` dans le fichier config.js. Le point de terminaison Gremlin doit correspondre uniquement au nom d’hôte, sans protocole/numéro de port comme `mygraphdb.graphs.azure.com` (et non `https://mygraphdb.graphs.azure.com` ou `mygraphdb.graphs.azure.com:433`).

    `config.endpoint = "GRAPHENDPOINT";`

3. Copiez la valeur de la clé primaire à partir du portail, puis définissez-la comme valeur du paramètre config.primaryKey dans le fichier config.js. Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

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

1. Ouvrez une fenêtre de terminal et remplacez l’élément par le répertoire d’installation pour le fichier package.json inclus dans le projet (via la commande `cd`).  

2. Exécutez `npm install` pour installer les modules npm requis, y compris `gremlin-secure`.

3. Exécutez `node app.js` sur un terminal pour démarrer votre application Node.

## <a name="browse-with-data-explorer"></a>Navigation avec l’Explorateur de données

Vous pouvez maintenant revenir à l’Explorateur de données, dans le portail Azure, pour afficher, modifier, interroger et manipuler vos nouvelles données graphiques.

Dans l’Explorateur de données, la nouvelle base de données apparaît dans le volet **Collections**. Développez **graphdb**, **graphcoll**, puis cliquez sur **Graph**.

Les données générées par l’exemple d’application s’affichent dans le volet **Graphiques**.

## <a name="review-slas-in-the-azure-portal"></a>Examiner les SLA dans le Portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Nettoyage des ressources

Si vous n’envisagez pas de continuer à utiliser cette application, supprimez toutes les ressources que vous avez créées dans cet article en procédant comme suit : 

1. Dans le menu de navigation de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, saisissez le nom de la ressource à supprimer, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer un compte Azure Cosmos DB, à créer un graphique à l’aide de l’Explorateur de données et à exécuter une application. Vous pouvez maintenant générer des requêtes plus complexes et implémenter une logique de traversée de graphique puissante, à l’aide de Gremlin. 

> [!div class="nextstepaction"]
> [Interroger à l’aide de Gremlin](tutorial-query-graph.md)

