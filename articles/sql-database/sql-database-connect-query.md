---
title: "Connexion à Azure SQL Database avec Visual Studio | Microsoft Docs"
description: "Découvrez comment vous connecter à une base de données SQL Azure dans Visual Studio."
services: sql-database
keywords: "se connecter à la base de données"
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 6789662847197ff3abb44df2d65a8ab761dfc1e7
ms.lasthandoff: 03/04/2017


---
# <a name="connect-to-a-sql-database-with-visual-studio"></a>Se connecter à la base de données SQL avec Visual Studio

Découvrez comment vous connecter à une base de données SQL Azure dans Visual Studio. 

## <a name="prerequisites"></a>Composants requis
Pour vous connecter à la base de données SQL à l’aide de Visual Studio, vous avez besoin des éléments suivants : 

* Une base de données SQL à laquelle se connecter. Cet article utilise l’exemple de base de données **AdventureWorks** . Pour obtenir l’exemple de base de données AdventureWorks, consultez [Créez la base de données de démonstration](sql-database-get-started.md).
* Visual Studio 2013 Update 4 (ou version ultérieure). Microsoft propose désormais Visual Studio Community *gratuitement*.
  
  * [Visual Studio Community, téléchargement](http://www.visualstudio.com/products/visual-studio-community-vs)
  * [Plus d’options gratuites Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)

## <a name="open-visual-studio-from-the-azure-portal"></a>Ouvrez Visual Studio à partir du portail Azure
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **More Services (Plus de services)** > **Bases de données SQL**
3. Ouvrez le panneau de la base de données **AdventureWorks** en localisant la base de données *AdventureWorks* , puis en cliquant dessus.
4. Cliquez sur le bouton **Outils** en haut du panneau de la base de données :
   
    ![Nouvelle requête. Se connecter à un serveur de base de données SQL : SQL Server Management Studio](./media/sql-database-connect-query/tools.png)
5. Cliquez sur **Ouvrir dans Visual Studio** (si vous avez besoin de Visual Studio, cliquez sur le lien de téléchargement) :
   
    ![Nouvelle requête. Se connecter à un serveur de base de données SQL : SQL Server Management Studio](./media/sql-database-connect-query/open-in-vs.png)
6. Visual Studio s’ouvre sur la fenêtre **Se connecter au serveur** déjà configurée pour se connecter au serveur et à la base de données sélectionnés dans le portail.  (Cliquez sur **Options** pour vous assurer que la connexion est définie sur la bonne base de données.) Saisissez votre mot de passe d’administrateur du serveur et cliquez sur **Connexion**.

    ![Nouvelle requête. Se connecter à un serveur de base de données SQL : SQL Server Management Studio](./media/sql-database-connect-query/connect.png)


1. Si vous n’avez pas de règle de pare-feu définie pour l’adresse IP de votre ordinateur, le message *Impossible de se connecter* s’affiche. Pour créer une règle de pare-feu, consultez [Configurer une règle de pare-feu au niveau du serveur sur une base de données SQL Azure](sql-database-configure-firewall-settings.md).
2. Une fois la connexion établie, la fenêtre de **l’Explorateur d’objets SQL Server** s’ouvre avec une connexion à votre base de données.
   
    ![Nouvelle requête. Se connecter à un serveur de base de données SQL : SQL Server Management Studio](./media/sql-database-connect-query/sql-server-object-explorer.png)

## <a name="run-a-sample-query"></a>Exécuter un exemple de requête
Vous voici connecté à la base de données. Consultez à présent les étapes suivantes pour savoir comment exécuter une requête simple :

1. Cliquez avec le bouton droit sur la base de données et sélectionnez **Nouvelle requête**.
   
    ![Nouvelle requête. Se connecter à un serveur de base de données SQL : SQL Server Management Studio](./media/sql-database-connect-query/new-query.png)
2. Dans la fenêtre de requête, copiez et collez le code suivant :
   
        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;
3. Cliquez sur le bouton **Exécuter** pour exécuter la requête :
   
    ![Vous avez réussi ! Se connecter au serveur de base de données SQL : Visual Studio](./media/sql-database-connect-query/run-query.png)

## <a name="next-steps"></a>Étapes suivantes
* L’ouverture des bases de données SQL dans Visual Studio se fait à l’aide de SQL Server Data Tools. Pour plus d’informations, consultez [SQL Server Data Tools](https://msdn.microsoft.com/library/hh272686.aspx).
* Pour vous connecter à une base de données SQL à l’aide d’un code, consultez [Connexion à SQL Database à l’aide de .NET (C#))](sql-database-develop-dotnet-simple.md).


