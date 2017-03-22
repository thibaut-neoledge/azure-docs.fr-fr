---
title: "Portail Azure : Créer et interroger une base de données SQL unique | Microsoft Docs"
description: "Découvrez comment créer un serveur logique SQL Database, une règle de pare-feu au niveau du serveur et des bases de données dans le Portail Azure. Vous apprendrez également à interroger une base de données SQL Azure à l’aide du portail Azure."
keywords: "didacticiel sur la base de données sql, créer une base de données sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 248885d041378301c5202263d24fdc9609e293d6
ms.lasthandoff: 03/15/2017


---
# <a name="create-and-query-a-single-azure-sql-database-in-the-azure-portal"></a>Créer et interroger une base de données SQL Azure unique dans le portail Azure

Les bases de données SQL Azure peuvent être créées via le portail Azure. Cette méthode fournit une interface utilisateur basée sur navigateur pour créer et configurer des bases de données SQL Azure et toutes les ressources Azure liées.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Créer une base de données SQL

Une base de données SQL Azure est créée dans un groupe de ressources Azure, sur un serveur logique et avec un ensemble défini de ressources de calcul et de stockage. Suivez ces étapes pour créer une base de données SQL contenant les exemples de données Adventure Works LT. 

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Bases de données** dans le panneau **Nouveau**, puis **Base de données SQL** dans le panneau **Bases de données**.

3. Remplissez le formulaire SQL Database avec les informations requises : 
   - Nom de la base de données : entrez un nom pour la base de données
   - Abonnement : sélectionnez votre abonnement
   - Groupe de ressources : sélectionnez un nouveau groupe ou un groupe existant
   - Source : sélectionnez **Sample (AdventureWOrksLT)** (Exemple (AdventureWOrksLT))
   - Serveur : créez un serveur (le nom du **serveur** doit être globalement unique)
   - Pool élastique : sélectionnez **Not now** (Pas maintenant) pour ce démarrage rapide
   - Niveau tarifaire : sélectionnez **20 DTU** et **250** Go de stockage
   - Classement : vous ne pouvez pas modifier cette valeur lors de l’importation de la base de données exemple 
   - Épingler au tableau de bord : cochez cette case

      ![créer une base de données](./media/sql-database-get-started/create-database-s1.png)

4. Lorsque vous avez terminé, cliquez sur **Créer**. L’approvisionnement prend quelques minutes.
5. Une fois le déploiement de la base de données SQL terminé, sélectionnez les **bases de données SQL** dans le tableau de bord ou en sélectionnant **Bases de données SQL** dans le menu de gauche et en cliquant sur votre nouvelle base de données sur la page **Bases de données SQL**. Une page de présentation de votre base de données s’ouvre, affiche le nom de serveur complet (tel que **mynewserver20170313.database.windows.net**) et fournit des options pour poursuivre la configuration.

      ![nouvelle base de données sql](./media/sql-database-get-started/new-database-s1-overview.png) 

## <a name="create-a-server-level-firewall-rule"></a>créer une règle de pare-feu au niveau du serveur ;

Le service SQL Database crée un pare-feu empêchant les applications et les outils externes de se connecter à votre nouveau serveur et à votre nouvelle base de données. Suivez ces étapes pour créer une règle de pare-feu au niveau du serveur SQL Database pour votre adresse IP afin de permettre la connectivité externe via le pare-feu de base de données SQL. 

1. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils de votre base de données. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre. 

      ![règle de pare-feu de serveur](./media/sql-database-get-started/server-firewall-rule.png) 

2. Cliquez sur **Ajouter une adresse IP cliente** dans la barre d’outils, puis cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle.

3. Cliquez sur **OK**, puis cliquez sur la **X** pour fermer la page Paramètres de pare-feu.

Vous pouvez maintenant vous connecter à la base de données et à son serveur à l’aide de SQL Server Management Studio ou tout autre outil de votre choix.

## <a name="query-the-sql-database"></a>Interroger la base de données SQL

Suivez ces étapes pour interroger la base de données à l’aide de l’éditeur de requêtes dans le portail Azure. 

1. Sur la page SQL Database de votre base de données, cliquez sur **Outils** dans la barre d’outils. La page d’aperçu de **l’éditeur de requêtes** s’ouvre.

     ![menu Outils](./media/sql-database-get-started/tools-menu.png) 

2. Cliquez sur **Éditeur de requêtes (version préliminaire)**, cochez la case **Conditions d’utilisation de la version préliminaire**, puis cliquez sur **OK**. L’éditeur de requêtes s’ouvre.

3. Cliquez sur **Connexion**, puis, lorsque vous y êtes invité, sélectionnez **Authentification du serveur SQL Server** et indiquez l’identifiant et le mot de passe de connexion administrateur.
4. Cliquez sur **OK** pour vous connecter.

5. Après vous être authentifié, tapez la requête de votre choix dans la fenêtre de requête, par exemple :

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Cliquez sur **Exécuter**, puis passez en revue les résultats de la requête dans le volet **Résultats**.

    ![query editor results](./media/sql-database-get-started/query-editor-results.png)

7. Cliquez sur la **X** pour fermer la page de l’éditeur de requêtes.

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez choisir d’enregistrer les ressources créées dans ce démarrage rapide pour d’autres didacticiels. Sinon, vous pouvez suivre les étapes suivantes pour supprimer toutes les ressources créées par ce démarrage rapide dans le portail Azure.

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myResourceGroup**. 
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myResourceGroup** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour vous connecter et envoyer des requêtes à l’aide de SQL Server Management Studio, consultez [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md).
- Pour vous connecter à l’aide de Visual Studio, consultez [Se connecter à la base de données SQL avec Visual Studio](sql-database-connect-query.md).
- Pour une présentation technique de SQL Database, consultez [Définition de la base de données SQL Présentation de SQL Database](sql-database-technical-overview.md).

