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
ms.custom: quick start create, mvc
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 05/30/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: ab664eb8803037b0bf09399bf9d28059fec2e204
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


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

   | Paramètre       | Valeur suggérée | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nom de la base de données** | mySampleDatabase | Pour les noms de base de données valides, consultez [Database Identifiers](https://docs.microsoft.com/en-us/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données). | 
   | **Abonnement** | Votre abonnement  | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources**  | myResourceGroup | Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Source** | Exemple (AdventureWorksLT) | Charge le schéma et les données AdventureWorksLT schéma dans votre nouvelle base de données |

   > [!IMPORTANT]
   > Vous devez sélectionner l’exemple de base de données sur ce formulaire, car il est utilisé dans le reste de ce guide de démarrage rapide.
   > 

4. Remplissez le formulaire de serveur SQL Server (serveur logique) avec les informations suivantes, comme indiqué dans l’illustration précédente :   

   | Paramètre       | Valeur suggérée | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nom du serveur** | Nom globalement unique | Pour les noms de serveur valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). | 
   | **Connexion d’administrateur du serveur** | Nom valide | Pour les noms de connexion valides, consultez [Database Identifiers](https://docs.microsoft.com/en-us/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données). |
   | **Mot de passe** | Mot de passe valide | Votre mot de passe doit comporter au moins 8 caractères et contenir des caractères appartenant à trois des catégories suivantes : caractères en majuscules, caractères en minuscules, chiffres et caractères non alphanumériques. |
   | **Abonnement** | Votre abonnement | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources** | myResourceGroup | Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Emplacement** | Emplacement valide | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |

   > [!IMPORTANT]
   > La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce guide de démarrage rapide pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure. 
   >  

   ![create database-server](./media/sql-database-get-started-portal/create-database-server.png)

5. Une fois le formulaire rempli, cliquez sur **Sélectionner**.

6. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service et le niveau de performances pour votre nouvelle base de données. Utilisez le curseur pour sélectionner **20 DTU** et **250** Go de stockage. Pour plus d’informations sur les DTU, consultez la page [Qu’est-ce qu’un DTU ?](sql-database-what-is-a-dtu.md).

   ![create database-s1](./media/sql-database-get-started-portal/create-database-s1.png)

7. Après avoir sélectionné le nombre de DTU, cliquez sur **Appliquer**.  

8. Maintenant que vous avez rempli le formulaire SQL Database, cliquez sur **Créer** pour approvisionner la base de données. L’approvisionnement prend quelques minutes. 

9. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

   ![notification](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>créer une règle de pare-feu au niveau du serveur ;

Le service SQL Database crée un pare-feu au niveau du serveur qui empêche les applications et les outils externes de se connecter au serveur ou à toute base de données sur le serveur, sauf si une règle de pare-feu est créée pour ouvrir le pare-feu à des adresses IP spécifiques. Suivez ces étapes pour créer une [règle de pare-feu au niveau du serveur de base de données SQL](sql-database-firewall-configure.md) pour l’adresse IP de votre client afin de permettre la connectivité externe via le pare-feu de base de données SQL pour votre adresse IP uniquement. 

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

1. Une fois le déploiement terminé, cliquez sur **Bases de données SQL** dans le menu de gauche, puis cliquez sur **mySampleDatabase** sur la page **Bases de données SQL**. La page de présentation de votre base de données s’ouvre, affiche le nom de serveur complet (tel que **mynewserver20170313.database.windows.net**) et fournit des options pour poursuivre la configuration. Copiez ce nom de serveur complet pour une utilisation ultérieure.

   > [!IMPORTANT]
   > Vous avez besoin du nom complet du serveur pour vous connecter à votre serveur et à ses bases de données dans les guides de démarrage rapide suivants.
   > 

   ![nom du serveur](./media/sql-database-get-started-portal/server-name.png) 

2. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils, comme illustré sur l’image précédente. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre. 

   ![règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule.png) 

3. Dans la barre d’outils, cliquez sur **Ajouter une adresse IP cliente** afin d’ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu. Une règle de pare-feu peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

4. Cliquez sur **Save**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle et ouvre le port 1433 sur le serveur logique.

   ![définir la règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. Cliquez sur **OK**, puis fermez la page **Paramètres de pare-feu**.

Vous pouvez maintenant vous connecter au serveur SQL Database et à ses bases de données à l’aide de SQL Server Management Studio ou de tout autre outil de votre choix à partir de cette adresse IP à l’aide du compte Administrateur de serveur créé au préalable.

> [!IMPORTANT]
> Par défaut, l’accès via le pare-feu SQL Database est activé pour tous les services Azure. Cliquez sur **ÉTEINT** sur cette page pour le désactiver pour tous les services Azure.
>

## <a name="query-the-sql-database"></a>Interroger la base de données SQL

Maintenant que vous avez créé un exemple de base de données dans Azure, nous allons utiliser l’outil de requête intégré au portail Azure pour vérifier que vous pouvez vous connecter à la base de données et demander des données. 

1. Sur la page SQL Database de votre base de données, cliquez sur **Outils** dans la barre d’outils. La page **Outils** s’ouvre.

   ![menu Outils](./media/sql-database-get-started-portal/tools-menu.png) 

2. Cliquez sur **Éditeur de requêtes (version préliminaire)**, cochez la case **Conditions d’utilisation de la version préliminaire**, puis cliquez sur **OK**. La page de l’éditeur de requêtes s’ouvre.

3. Cliquez sur **Connexion**, puis, lorsque vous y êtes invité, sélectionnez **Authentification du serveur SQL Server** et indiquez l’identifiant et le mot de passe de connexion administrateur créés précédemment.

   ![se connecter](./media/sql-database-get-started-portal/login.png) 

4. Cliquez sur **OK** pour vous connecter.

5. Après vous être authentifié, saisissez la requête suivante dans le volet de l’éditeur de requête.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Cliquez sur **Exécuter**, puis passez en revue les résultats de la requête dans le volet **Résultats**.

   ![query editor results](./media/sql-database-get-started-portal/query-editor-results.png)

7. Fermez la page **Éditeur de requêtes** et la page **Outils**.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez pas besoin de ces ressources pour un autre didacticiel de démarrage rapide (voir [Étapes suivantes](#next-steps)), vous pouvez les supprimer en procédant comme suit :


1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myResourceGroup**. 
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myResourceGroup** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’une base de données, vous pouvez vous connecter et interroger à l’aide de vos outils préférés. En savoir plus en choisissant votre outil ci-dessous :

- [SQL Server Management Studio](sql-database-connect-query-ssms.md)
- [Visual Studio Code](sql-database-connect-query-vscode.md)
- [.NET](sql-database-connect-query-dotnet.md)
- [PHP](sql-database-connect-query-php.md)
- [Node.JS](sql-database-connect-query-nodejs.md)
- [Java](sql-database-connect-query-java.md)
- [Python](sql-database-connect-query-python.md)
- [Ruby](sql-database-connect-query-ruby.md)

