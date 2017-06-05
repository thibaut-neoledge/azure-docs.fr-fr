---
title: "Azure CosmosDB : Créer une application web avec .NET Core et API DocumentDB | Microsoft Docs"
description: "Cet article présente un exemple de code .NET Core que vous pouvez utiliser pour vous connecter à l’API DocumentDB d’Azure Cosmos DB, et pour l’interroger."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 9c0806a9edb52f0c05cced55edbda6b207398e3c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---
# <a name="azure-cosmos-db-build-a-documentdb-api-web-app-with-net-core-and-the-azure-portal"></a>Azure Cosmos DB : Créer une application web API DocumentDB avec .NET Core et le Portail Azure

Azure Cosmos DB est un service de base de données multi-modèles mondialement distribué par Microsoft. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer, à l’aide du Portail Azure, un compte Azure Cosmos DB, une base de données de documents, ainsi qu’une collection. Vous allez ensuite créer et déployer une application web de liste de tâches, basée sur [l’API .NET Core DocumentDB](documentdb-introduction.md), comme illustré par la capture d’écran suivante. 

![Application To-Do avec des exemples de données](./media/create-documentdb-dotnet-core/azure-cosmosdb-todo-app-list.png)

## <a name="prerequisites"></a>Composants requis

Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-net-core"></a>Installez .NET Core

Installez .NET Core en suivant les instructions sur la page [Kit de développement logiciel .NET Core](https://www.microsoft.com/net/download/core). Les kits de développement logiciel sont disponibles pour Windows, macOS, et Linux.

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Ajouter une collection

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Ajouter un exemple de données

[!INCLUDE [cosmos-db-create-sample-data](../../includes/cosmos-db-create-sample-data.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Nous allons maintenant cloner une application API DocumentDB à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données.

1. Ouvrez une fenêtre de terminal git, comme git bash, et accédez à un répertoire de travail à l’aide de la commande `CD`.  

2. Exécutez la commande suivante pour cloner l’exemple de référentiel. 

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

3. Ouvrez le fichier de solution dans Visual Studio. 
    
## <a name="review-the-code"></a>Examiner le code

[!INCLUDE [cosmos-db-tutorial-review-code-dotnet](../../includes/cosmos-db-tutorial-review-code-dotnet.md)]

## <a name="update-your-connection-string"></a>Mettre à jour votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Dans le [portail Azure](http://portal.azure.com/), dans votre compte Azure Cosmos DB, dans le volet de navigation de gauche, cliquez sur **Clés**, puis sur **Clés en lecture-écriture**. Vous utiliserez les boutons Copier sur le côté droit de l’écran pour copier l’URI et la clé primaire dans le fichier web.config à l’étape suivante.

    ![Affichage et copie d’une clé d’accès rapide dans le portail Azure, panneau Clés](./media/create-documentdb-dotnet-core/keys.png)

2. Dans Visual Studio 2017, ouvrez le fichier web.config. 

3. Copiez votre valeur URI à partir du portail (à l’aide du bouton Copier) et définissez-la comme la valeur de la clé du point de terminaison dans le fichier web.config. 

    `<add key="endpoint" value="FILLME" />`

4. Puis, copiez votre valeur de clé primaire à partir du portail et définissez-la comme la valeur de la authKey dans web.config. 

    `<add key="authKey" value="FILLME" />`

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

## <a name="run-the-web-app"></a>Exécuter l’application web

1. Dans Visual Studio, cliquez avec le bouton droit sur le nom du projet dans l’**Explorateur de solutions**, puis cliquez sur **Gérer les packages NuGet**. 

2. Dans la zone **Parcourir** de NuGet, tapez *DocumentDB*.

3. À partir des résultats, installez la bibliothèque **Microsoft Azure DocumentDB**. Cette opération permet d’installer le package Azure Cosmos DB ainsi que toutes les dépendances.

4. Appuyez sur Ctrl + F5 pour exécuter l'application. Votre application s’affiche dans votre navigateur. 

5. Cliquez sur **Créer** dans le navigateur et créer quelques tâches dans votre application To-Do.

   ![Application To-Do avec des exemples de données](./media/create-documentdb-dotnet-core/azure-cosmosdb-todo-app-list.png)

Vous pouvez dès à présent revenir à l’Explorateur de données et voir la requête, modifier et travailler avec ces nouvelles données. 

## <a name="review-slas-in-the-azure-portal"></a>Examiner les SLA dans le Portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées durant ce guide de démarrage rapide dans le Portail Azure en procédant de la façon suivante :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer une collection à l’aide de l’Explorateur de données et à exécuter une application web. Vous pouvez maintenant importer des données supplémentaires à votre compte Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB](import-data.md)

