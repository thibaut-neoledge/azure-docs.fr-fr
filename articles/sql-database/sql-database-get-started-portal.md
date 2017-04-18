---
title: "Portail Azure : Créer une base de données SQL | Microsoft Docs"
description: "Découvrez comment créer un serveur logique SQL Database, une règle de pare-feu au niveau du serveur et des bases de données dans le Portail Azure. Vous apprendrez également à interroger une base de données SQL Azure à l’aide du portail Azure."
keywords: "didacticiel sur la base de données sql, créer une base de données sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 04/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 58af25d90b419b3ddb986118a8c9ba3b42aa95a6
ms.lasthandoff: 04/12/2017


---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Création d’une base de données SQL Azure à l’aide du portail Azure

Ce didacticiel de démarrage rapide vous guide dans la procédure de création d’une base de données SQL dans Azure. Azure SQL Database est une offre de type « base de données en tant que service » qui vous permet d’exécuter et de mettre à l’échelle des bases de données SQL Server hautement disponibles dans le cloud. Ce guide de démarrage rapide vous indique comment commencer par créer une base de données SQL à l’aide du Portail Azure.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Créer une base de données SQL

Une base de données SQL Azure est créée avec un ensemble défini de [ressources de calcul et de stockage](sql-database-service-tiers.md). La base de données est créée dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) et dans un [serveur logique Azure SQL Database](sql-database-features.md). 

Suivez ces étapes pour créer une base de données SQL contenant les exemples de données Adventure Works LT. 

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Bases de données** dans la page **Nouveau**, puis **Base de données SQL** dans la page **Bases de données**.

    ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

3. Remplissez le formulaire de base de données SQL avec les informations suivantes, comme indiqué dans l’illustration précédente :     
   - Nom de la base de données : **mySampleDatabase**
   - Groupe de ressources : **myResourceGroup**
   - Source : **Exemple (AdventureWorksLT)**

4. Cliquez sur **Serveur** pour créer et configurer le serveur pour votre nouvelle base de données. Remplissez le **formulaire Nouveau serveur** en spécifiant un nom de serveur global unique, fournissez un nom pour la connexion d’administrateur serveur et spécifiez le mot de passe de votre choix. 

    ![create database-server](./media/sql-database-get-started-portal/create-database-server.png)
5. Cliquez sur **Sélectionner**.

6. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service et le niveau de performances pour votre nouvelle base de données. Pour ce démarrage rapide, sélectionnez **20 DTU** et **250** Go de stockage

    ![create database-s1](./media/sql-database-get-started-portal/create-database-s1.png)

7. Cliquez sur **Apply**.  

8. Cliquez sur **Créer** pour approvisionner la base de données. L’approvisionnement prend quelques minutes. 

9. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

    ![notification](./media/sql-database-get-started-portal/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>créer une règle de pare-feu au niveau du serveur ;

Le service SQL Database crée un pare-feu au niveau du serveur qui empêche les applications et les outils externes de se connecter au serveur ou à toute base de données sur le serveur, sauf si une règle de pare-feu est créée pour ouvrir le pare-feu à des adresses IP spécifiques. Suivez ces étapes pour créer une [règle de pare-feu au niveau du serveur de base de données SQL](sql-database-firewall-configure.md) pour l’adresse IP de votre client afin de permettre la connectivité externe via le pare-feu de base de données SQL pour votre adresse IP uniquement. 

1. Une fois le déploiement terminé, cliquez sur **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données sur la page **Bases de données SQL**. La page de présentation de votre base de données s’ouvre, affiche le nom de serveur complet (tel que **mynewserver20170327.database.windows.net**) et fournit des options pour poursuivre la configuration.

      ![règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils, comme illustré sur l’image précédente. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre. 

3. Cliquez sur **Ajouter une adresse IP cliente** dans la barre d’outils, puis cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle.

      ![définir la règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. Cliquez sur **OK**, puis fermez la page **Paramètres de pare-feu**.

Vous pouvez maintenant vous connecter au serveur SQL Database et à ses bases de données à l’aide de SQL Server Management Studio ou de tout autre outil de votre choix à partir de cette adresse IP à l’aide du compte Administrateur de serveur créé au préalable.

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par votre pare-feu réseau. Dans ce cas, vous ne pourrez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

## <a name="query-the-sql-database"></a>Interroger la base de données SQL

Lorsque nous avons créé notre base de données SQL, nous l’avons rempli avec l’exemple de base de données **AdventureWorksLT** (qui faisait partie des options sélectionnées lors de l’étape précédente de création de l’interface utilisateur dans ce guide de démarrage rapide). Nous allons maintenant utiliser l’outil de requête intégré au portail Azure pour interroger les données. 

1. Sur la page SQL Database de votre base de données, cliquez sur **Outils** dans la barre d’outils. La page **Outils** s’ouvre.

     ![menu Outils](./media/sql-database-get-started-portal/tools-menu.png) 

2. Cliquez sur **Éditeur de requêtes (version préliminaire)**, cochez la case **Conditions d’utilisation de la version préliminaire**, puis cliquez sur **OK**. La page de l’éditeur de requêtes s’ouvre.

3. Cliquez sur **Connexion**, puis, lorsque vous y êtes invité, sélectionnez **Authentification du serveur SQL Server** et indiquez l’identifiant et le mot de passe de connexion administrateur créés précédemment.

    ![se connecter](./media/sql-database-get-started-portal/login.png) 

4. Cliquez sur **OK** pour vous connecter.

5. Après vous être authentifié, saisissez la requête suivante dans le volet de l’éditeur de requête.

   ```
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Cliquez sur **Exécuter**, puis passez en revue les résultats de la requête dans le volet **Résultats**.

    ![query editor results](./media/sql-database-get-started-portal/query-editor-results.png)

7. Fermez la page **Éditeur de requêtes** et la page **Outils**.

## <a name="clean-up-resources"></a>Supprimer des ressources

Les autres démarrages rapides de cette collection reposent sur ce démarrage rapide. Si vous souhaitez continuer à utiliser d’autres démarrages rapides, ne nettoyez pas les ressources créées dans ce démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées par ce démarrage rapide dans le portail Azure.

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myResourceGroup**. 
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myResourceGroup** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour vous connecter et envoyer des requêtes à l’aide de SQL Server Management Studio, consultez [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md).
- Pour vous connecter et exécuter des requêtes à l’aide de Visual Studio Code, consultez l’article décrivant comment [se connecter et effectuer des requêtes avec Visual Studio Code](sql-database-connect-query-vscode.md).
- Pour vous connecter et exécuter des requêtes à l’aide de .NET, consultez l’article décrivant comment [se connecter et effectuer des requêtes avec .NET](sql-database-connect-query-dotnet.md).
- Pour vous connecter et exécuter des requêtes à l’aide de PHP, consultez l’article décrivant comment [se connecter et effectuer des requêtes avec PHP](sql-database-connect-query-php.md).
- Pour vous connecter et exécuter des requêtes à l’aide de Node.js, consultez l’article décrivant comment [se connecter et effectuer des requêtes avec Node.js](sql-database-connect-query-nodejs.md).
- Pour vous connecter et exécuter des requêtes à l’aide de Java, consultez l’article décrivant comment [se connecter et effectuer des requêtes avec Java](sql-database-connect-query-java.md).
- Pour vous connecter et exécuter des requêtes à l’aide de Python, consultez l’article décrivant comment [se connecter et effectuer des requêtes avec Python](sql-database-connect-query-python.md).
- Pour vous connecter et exécuter des requêtes à l’aide de Ruby, consultez l’article décrivant comment [se connecter et effectuer des requêtes avec Ruby](sql-database-connect-query-ruby.md).

