---
title: "Comment créer un compte Azure Cosmos DB | Microsoft Docs"
description: "Créer une base de données NoSQL avec Azure Cosmos DB. Suivez ces instructions pour créer un compte Azure Cosmos DB., puis pour créer votre base de données NoSQL ultrarapide et de portée internationale."
keywords: "créer une base de données"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 0e7f8488-7bb7-463e-b6fd-3ae91a02c03a
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/17/2017
ms.author: mimig
redirect_url: https://aka.ms/acdbnetqa
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 13621d942f2880f4dd1523315f43099eca2607d8
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-create-an-azure-cosmos-db-nosql-account-using-the-azure-portal"></a>Comment créer un compte NoSQL Azure Cosmos DB via le portail Azure
> [!div class="op_single_selector"]
> * [Portail Azure](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

Pour créer une base de données avec Microsoft Azure Cosmos DB, vous devez :

* Avoir un compte Azure. Si vous n’en avez pas, vous pouvez obtenir un [compte Azure gratuit](https://azure.microsoft.com/free) .
* Créez un compte Azure Cosmos DB.  

Vous pouvez créer un compte Azure Cosmos DB avec le Portail Azure, à l’aide de modèles Azure Resource Manager ou de l’interface de ligne de commande Azure (CLI). Cet article décrit la procédure de création d’un compte Azure Cosmos DB à l’aide du Portail Azure. Pour créer un compte à l’aide d’Azure Resource Manager ou de l’interface de ligne de commande Azure, consultez l’article [Automatisation de la création de compte de base de données Azure Cosmos DB](documentdb-automation-resource-manager-cli.md).

Vous ne connaissez pas Azure Cosmos DB ? Regardez [cette](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) vidéo de quatre minutes réalisée par Scott Hanselman pour voir comment effectuer les tâches les plus courantes dans le portail en ligne.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le panneau de navigation gauche, cliquez sur **Nouveau**, **Bases de données**, puis sur **Azure Cosmos DB**.

   ![Capture d’écran du portail Azure, mettant en surbrillance l’option Plus de services, et NoSQL (Azure Cosmos DB))](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-1.png)  
3. Dans le panneau **Nouveau compte**, indiquez la configuration souhaitée pour le compte Azure Cosmos DB.

    ![Capture d’écran du nouveau panneau Azure Cosmos DB](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-2.png)

   * Dans la zone **ID**, entrez un nom pour identifier le compte Azure Cosmos DB.  Lorsque **l’ID** est validé, une coche verte s’affiche dans la case **ID**. La valeur de **ID** devient le nom d’hôte dans l’URI. Cet **ID** ne peut contenir que des minuscules, des chiffres, le caractère « - » et doit compter entre 3 et 50 caractères. Notez que *documents.azure.com* est ajouté au nom du point de terminaison de votre choix. Celui-ci devient le point de terminaison de votre compte Azure Cosmos DB.
   * Dans la zone **API NoSQL** , sélectionnez le modèle de programmation à utiliser :

     * **DocumentDB** : l’API DocumentDB est disponible via les [Kits de développement logiciel (SDK)](documentdb-sdk-dotnet.md) .NET, Java, Node.js, Python et JavaScript, ainsi que HTTP [REST](https://msdn.microsoft.com/library/azure/dn781481.aspx), et offre un accès par programme à toutes les fonctionnalités de DocumentDB.
     * **MongoDB** : DocumentDB propose également la [prise en charge au niveau du protocole](documentdb-protocol-mongodb.md) pour les API **MongoDB**. Lorsque vous choisissez l’option de l’API MongoDB, vous pouvez utiliser les Kits de développement logiciel (SDK) MongoDB et les [outils](documentdb-mongodb-mongochef.md) existants pour communiquer avec DocumentDB. Vous pouvez [déplacer](documentdb-import-data.md) vos applications MongoDB existantes pour utiliser DocumentDB, [sans modification de code nécessaire](documentdb-connect-mongodb-account.md), et tirer parti d’une base de données entièrement gérée en tant que service, avec mise à l’échelle illimitée, réplication globale et autres fonctionnalités.
   * Dans **Abonnement**, sélectionnez l’abonnement Azure à utiliser avec le compte Azure Cosmos DB. Si votre compte ne comporte qu’un seul abonnement, ce compte sera sélectionné par défaut.
   * Dans **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour votre compte Azure Cosmos DB.  Par défaut, un nouveau groupe de ressources est créé. Pour plus d’informations, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](../azure-portal/resource-group-portal.md).
   * Utilisez **Emplacement** pour indiquer l’emplacement géographique de l’hébergement de votre compte Azure Cosmos DB.
4. Une fois les options du nouveau compte Azure Cosmos DB configurées, cliquez sur **Créer**. Pour vérifier l’état du déploiement, vérifiez le hub de notification.  

   ![Création rapide de bases de données - Capture d’écran du hub de notifications, indiquant que le compte Azure Cosmos DB est en cours de création](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-4.png)  

   ![Capture d’écran du hub de notifications montrant le compte Azure Cosmos DB créé avec succès et déployé vers un groupe de ressources - notification du créateur de base de données en ligne](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-5.png)
5. Une fois le compte Azure Cosmos DB créé, il est immédiatement utilisable avec les paramètres par défaut. La cohérence par défaut du compte Azure Cosmos DB est définie sur **Par session**.  Vous pouvez modifier la cohérence par défaut en cliquant sur **Cohérence par défaut** dans le menu de ressources. Pour en savoir plus sur les niveaux de cohérence proposés par Azure Cosmos DB, consultez [Niveaux de cohérence dans Azure Cosmos DB](documentdb-consistency-levels.md).

   ![Capture d’écran du panneau Groupe de ressources - commencer le développement d’applications](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-6.png)  

   ![Capture d’écran du panneau Niveau de cohérence - Cohérence de session](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-7.png)  

[How to: Create an Azure Cosmos DB account]: #Howto
[Next steps]: #NextSteps


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous disposez d’un compte Azure Cosmos DB, l’étape suivante consiste à créer une collection et une base de données Azure Cosmos DB.

Pour créer une collection et une base de données, utilisez au choix :

* Le portail Azure, tel que décrit dans la rubrique [Création d’une collection Azure Cosmos DB avec le portail Azure](documentdb-create-collection.md).
* Les didacticiels complets, qui incluent des exemples de données : [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) ou [Python](documentdb-python-application.md).
* L’exemple de code [.NET](documentdb-dotnet-samples.md#database-examples), [Node.js](documentdb-nodejs-samples.md#database-examples) ou [Python](documentdb-python-samples.md#database-examples) disponible dans GitHub.
* Les Kits de développement logiciel (SDK) [.NET](documentdb-sdk-dotnet.md), [.NET Core](documentdb-sdk-dotnet-core.md), [Node.js](documentdb-sdk-node.md), [Java](documentdb-sdk-java.md), [Python](documentdb-sdk-python.md) et [REST](https://msdn.microsoft.com/library/azure/mt489072.aspx).

Après avoir créé la base de données et la collection, vous devez [ajouter des documents](documentdb-view-json-document-explorer.md) aux collections.

Une fois que vous avez des documents dans une collection, vous pouvez utiliser [DocumentDB SQL](documentdb-sql-query.md) pour [exécuter des requêtes](documentdb-sql-query.md#ExecutingSqlQueries) sur vos documents. Vous pouvez exécuter des requêtes à l’aide de [l’Explorateur de requêtes](documentdb-query-collections-query-explorer.md) dans le portail, [l’API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou l’un des [Kits de développement logiciel (SDK)](documentdb-sdk-dotnet.md).

### <a name="learn-more"></a>En savoir plus
Pour en savoir plus sur Azure Cosmos DB, explorez les ressources suivantes :

* [Azure Cosmos DB Introduction](../cosmos-db/introduction.md) (Présentation d’Azure Cosmos DB)

