---
title: Comment créer une base de données dans DocumentDB | Microsoft Docs
description: Découvrez comment créer une base de données à l’aide du portail du service en ligne pour Azure DocumentDB, votre base de données NoSQL globale et rapide.
keywords: création d’une base de données
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: mimig

---
# Création d’une base de données pour DocumentDB à l’aide du portail Azure
Pour utiliser Microsoft Azure DocumentDB, vous devez posséder un [compte DocumentDB](documentdb-create-account.md), une base de données, une collection et des documents. Cette rubrique décrit la procédure à suivre pour créer une base de données DocumentDB dans le portail Microsoft Azure. Pour plus d’informations sur la création d’une base de données à l’aide d’un des Kits de développement logiciel (SDK), consultez l’article [Autres méthodes de création d’une base de données DocumentDB](#other-ways-to-create-a-documentdb-database).

1. Dans la barre de lancement du [portail Azure](https://portal.azure.com/), cliquez sur **DocumentDB (NoSQL)**. Si l’option **DocumentDB (NoSQL)** n’est pas affichée, cliquez sur **Plus de services**, puis sur **DocumentDB (NoSQL)**.

    ![Capture d’écran montrant comment créer une base de données, et mettant en surbrillance l’option Comptes DocumentDB dans le panneau Parcourir et un compte DocumentDB dans le panneau Comptes DocumentDB](./media/documentdb-create-database/docdb-database-creation-1-2.png)

1. Dans le panneau **DocumentDB (NoSQL)**, sélectionnez le compte dans lequel une base de données NoSQL DocumentDB sera ajoutée. Si vous n'avez aucun compte répertorié, vous devrez [créer un compte DocumentDB](documentdb-create-account.md).
2. Dans le panneau **Compte DocumentDB**, cliquez sur **Ajouter une base de données**.
   
    ![Capture d’écran montrant comment créer une base de données, et mettant en surbrillance le bouton Ajouter une base de données, la zone ID et le bouton OK](./media/documentdb-create-database/docdb-database-creation-3-5.png)
3. Dans le panneau **Ajout d’une base de données**, saisissez l'ID de votre nouvelle base de données. Lorsque le nom est validé, une coche verte s’affiche dans la case **ID**. Cliquez ensuite sur **OK**.
   
    ![Capture d’écran montrant comment créer une base de données, et mettant en surbrillance le bouton Ajouter une base de données, la zone ID et le bouton OK](./media/documentdb-create-database/docdb-database-creation-4.png)
4. La nouvelle base de données apparaît désormais dans le filtre **Bases de données** dans le panneau **Compte DocumentDB**.
   
    ![Capture d'écran de la nouvelle base de données dans le panneau Compte DocumentDB](./media/documentdb-create-database/docdb-database-creation-6.png)

## Autres méthodes de création d'une base de données DocumentDB
Les bases de données ne doivent pas nécessairement être créées avec le portail. Vous pouvez également les créer avec les [Kits de développement logiciel (SDK) DocumentDB](documentdb-sdk-dotnet.md) ou l’[API REST](https://msdn.microsoft.com/library/mt489072.aspx). Pour en savoir plus sur l’utilisation des bases de données à l’aide du Kit de développement logiciel (SDK) .NET, consultez les [exemples de base de données .NET](documentdb-dotnet-samples.md#database-examples). Pour en savoir plus sur l’utilisation des bases de données à l’aide du Kit de développement logiciel (SDK) Node.js, consultez les [exemples de base de données Node.js](documentdb-nodejs-samples.md#database-examples).

## Étapes suivantes
Maintenant que vous savez comment créer une base de données DocumentDB, l’étape suivante consiste à [créer une collection](documentdb-create-collection.md).

Une fois votre collection créée, vous pouvez [ajouter des documents JSON](documentdb-view-json-document-explorer.md) à l’aide de l’Explorateur de documents du portail, [importer des documents](documentdb-import-data.md) dans la collection à l’aide de l’outil de migration de données DocumentDB ou encore utiliser l’un des [Kits de développement logiciel (SDK) DocumentDB](documentdb-sdk-dotnet.md) pour effectuer des opérations CRUD. DocumentDB possède les kits de développement logiciel (SDK) de .NET, Java, Python, Node.js et de l’API JavaScript. Pour obtenir des exemples de code .NET montrant comment créer, mettre à jour et supprimer des documents, consultez les [exemples de document .NET](documentdb-dotnet-samples.md#document-examples). Pour en savoir plus sur l’utilisation des documents à l’aide du Kit de développement logiciel (SDK) Node.js, consultez les [exemples de document Node.js](documentdb-nodejs-samples.md#document-examples).

Une fois que chaque collection contient des documents, vous pouvez utiliser [DocumentDB SQL](documentdb-sql-query.md) pour [exécuter des requêtes](documentdb-sql-query.md#executing-sql-queries) sur vos documents à l’aide de l’[Explorateur de requête](documentdb-query-collections-query-explorer.md) du portail, de l’[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de l’un des [Kits de développement logiciel](documentdb-sdk-dotnet.md).

<!----HONumber=AcomDC_0831_2016-->