---
title: "SSMS : Se connecter et interroger des données dans Azure SQL Database | Microsoft Docs"
description: "Découvrez comment vous connecter à Base de données SQL sur Azure avec SQL Server Management Studio (SSMS). Ensuite, exécutez des instructions Transact-SQL (T-SQL) pour interroger et modifier des données."
metacanonical: 
keywords: "connexion à une base de données sql, sql server management studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/15/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: a08fe566f4962684bc4aad220687e9cd36fc4abf
ms.lasthandoff: 04/17/2017


---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Azure SQL Database : utilisez SQL Server Management Studio pour vous connecter et exécuter des requêtes

[SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) est un outil de gestion vous permettant de créer et gérer des ressources SQL Server à partir de l’interface utilisateur ou de scripts. Ce guide de démarrage rapide indique comment utiliser SSMS pour se connecter à une base de données SQL Azure, puis utiliser les instructions Transact-SQL pour interroger, insérer, mettre à jour et supprimer des données dans la base de données. 

Ce guide de démarrage rapide utilise comme point de départ les ressources créées dans l’une de ces instructions de démarrage rapide :

- [Créer une base de données - Portail](sql-database-get-started-portal.md)
- [Créer une base de données - CLI](sql-database-get-started-cli.md)

Avant de commencer, assurez-vous que vous avez installé la toute dernière version de [SSMS](https://msdn.microsoft.com/library/mt238290.aspx). 

## <a name="get-connection-information"></a>Obtenir des informations de connexion

Obtenez le nom de serveur complet de votre serveur Azure SQL Database dans le portail Azure. Utilisez le nom de serveur complet pour vous connecter à votre serveur avec SQL Server Management Studio.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Sur la page **Vue d’ensemble** de votre base de données, vérifiez le nom complet du serveur, comme indiqué dans l’image ci-dessous. Vous pouvez pointer sur le nom du serveur pour afficher l’option **Cliquez pour copier**.

   ![informations de connexion](./media/sql-database-connect-query-ssms/connection-information.png) 

4. Si vous avez oublié vos informations de connexion à votre serveur Azure SQL Database, accédez à la page du serveur SQL Database pour afficher le nom de l’administrateur du serveur et, si nécessaire, réinitialiser le mot de passe. 

## <a name="connect-to-your-database-in-the-sql-database-logical-server"></a>Se connecter à votre base de données sur le serveur logique SQL Database

Utilisez SQL Server Management Studio pour établir une connexion à votre serveur de base de données SQL Azure. 

> [!IMPORTANT]
> Un serveur logique Azure SQL Database écoute sur le port 1433. Si vous essayez de vous connecter à un serveur logique Azure SQL Database à partir d’un pare-feu d’entreprise, ce port doit être ouvert dans le pare-feu d’entreprise pour que vous puissiez vous connecter.
>

1. Ouvrez SQL Server Management Studio.

2. Dans la fenêtre **Se connecter au serveur**, entrez les valeurs suivantes :
   - **Type de serveur** : spécifiez le moteur de base de données
   - **Nom du serveur** : entrez votre nom de serveur complet, par exemple **mynewserver20170313.database.windows.net**
   - **Authentification** : spécifiez l’authentification SQL Server
   - **Connexion** : entrez votre compte d’administrateur de serveur
   - **Mot de passe** : entrez le mot de passe de votre compte d’administrateur de serveur

   ![connect to server](./media/sql-database-connect-query-ssms/connect.png)  

3. Cliquez sur **Options** dans la boîte de dialogue **Se connecter au serveur**. Dans la section **Se connecter à la base de données**, entrez **mySampleDatabase** pour vous connecter à cette base de données.

   ![connexion à la base de données sur le serveur](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Cliquez sur **Connecter**. La fenêtre Explorateur d’objets s’ouvre dans SSMS. 

   ![connected to server](./media/sql-database-connect-query-ssms/connected.png)  

5. Dans l’Explorateur d’objets, développez **Bases de données**, puis **mySampleDatabase** pour afficher les objets dans la base de données exemple.

## <a name="query-data"></a>Données de requête

Utilisez l’instruction Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) pour interroger votre base de données SQL Azure.

1. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur **mySampleDatabase**, puis cliquez sur **Nouvelle requête**. Une fenêtre de requête vide connectée à votre base de données s’ouvre.
2. Dans la fenêtre de requête, entrez la requête suivante :

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Dans la barre d’outils, cliquez sur **Exécuter** pour récupérer des données à partir des tables Product et ProductCategory.

    ![query](./media/sql-database-connect-query-ssms/query.png)

## <a name="insert-data"></a>Insertion des données

Utilisez l’instruction Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) pour insérer des données dans votre base de données SQL Azure.

1. Dans la fenêtre de requête, remplacez la requête précédente par la requête suivante :

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Dans la barre d’outils, cliquez sur **Exécuter** pour insérer une nouvelle ligne dans la table Product.

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>Mise à jour des données

Utilisez l’instruction Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) pour mettre à jour des données dans votre base de données SQL Azure.

1. Dans la fenêtre de requête, remplacez la requête précédente par la requête suivante :

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Dans la barre d’outils, cliquez sur **Exécuter** pour mettre à jour la ligne spécifiée dans la table Product.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>Suppression de données

Utilisez l’instruction Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) pour supprimer des données dans votre base de données SQL Azure.

1. Dans la fenêtre de requête, remplacez la requête précédente par la requête suivante :

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Dans la barre d’outils, cliquez sur **Exécuter** pour supprimer la ligne spécifiée dans la table Product.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur SSMS, consultez [Utiliser SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Pour vous connecter et exécuter des requêtes à l’aide de Visual Studio Code, consultez l’article décrivant comment [se connecter et effectuer des requêtes avec Visual Studio Code](sql-database-connect-query-vscode.md).
- Pour vous connecter et exécuter des requêtes avec .NET, consultez la page [Se connecter et effectuer des requêtes avec .NET](sql-database-connect-query-dotnet.md).
- Pour vous connecter et exécuter des requêtes avec PHP, consultez la page [se connecter et effectuer des requêtes avec PHP](sql-database-connect-query-php.md).
- Pour vous connecter et exécuter des requêtes avec Node.js, consultez la page [se connecter et effectuer des requêtes avec Node.js](sql-database-connect-query-nodejs.md).
- Pour vous connecter et exécuter des requêtes avec Java, consultez la page [se connecter et effectuer des requêtes avec Java](sql-database-connect-query-java.md).
- Pour vous connecter et exécuter des requêtes avec Python, consultez la page [se connecter et effectuer des requêtes avec Python](sql-database-connect-query-python.md).
- Pour vous connecter et exécuter des requêtes avec Ruby, consultez la page [se connecter et effectuer des requêtes avec Ruby](sql-database-connect-query-ruby.md).

