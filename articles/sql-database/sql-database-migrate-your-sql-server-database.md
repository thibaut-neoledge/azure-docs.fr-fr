---
title: "Migrer une base de données SQL Server vers Azure SQL Database | Microsoft Docs"
description: "Apprenez à migrer votre base de données SQL Server vers Azure SQL Database."
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,load & move data
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/27/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 375d3ea0230e7d3fd0fc02ca7e0b8a7a76c24a27
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017

---

# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Migrer votre base de données SQL Server vers Azure SQL Database

Le déplacement de votre base de données SQL Server se déroule en trois phases : préparation, exportation, puis importation. Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparer une base de données dans SQL Server pour la migration vers la base de données SQL Azure à l’aide de [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA)
> * Exporter la base de données vers un fichier BACPAC
> * Importer un fichier BACPAC dans une base de données SQL Azure

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Composants requis

Pour suivre ce tutoriel, vérifiez que les conditions préalables suivantes sont bien satisfaites :

- La dernière version de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) est installée. L’installation de SSMS installe également la version la plus récente de SQLPackage, un utilitaire en ligne de commande qui peut être utilisé pour automatiser de nombreuses tâches de développement de bases de données. 
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) est installé.
- Vous avez identifié une base de données à migrer, à laquelle vous avez accès. Ce didacticiel utilise la [base de données OLTP AdventureWorks SQL Server 2008 R2](https://msftdbprodsamples.codeplex.com/releases/view/59211) sur une instance de SQL Server 2008 R2 ou version ultérieure, mais vous pouvez utiliser une base de données de votre choix. Pour résoudre des problèmes de compatibilité, utilisez [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).

## <a name="prepare-for-migration"></a>Préparation de la migration

Vous êtes prêt à préparer la migration. Effectuez les étapes suivantes pour utiliser **[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)** pour évaluer la disponibilité de votre base de données pour la migration vers Azure SQL Database.

1. Ouvrez **Data Migration Assistant**. Vous pouvez exécuter DMA sur n’importe quel ordinateur disposant d’une connectivité à l’instance SQL Server contenant la base de données que vous projetez de migrer ; vous n’avez pas besoin de l’installer sur l’ordinateur qui héberge l’instance SQL Server.

     ![ouvrir data migration assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. Dans le menu de gauche, cliquez sur **+ Nouveau** pour créer un projet **Évaluation**. Remplissez le formulaire avec une valeur pour **Nom du projet** (tous les autres paramètres doivent conserver leurs valeurs par défaut) et cliquez sur **Créer**. La page **Options** s’ouvre.

     ![nouveau projet de data migration assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3. Dans la page **Options**, cliquez sur **Suivant**. La page **Select sources** (Sélectionner des sources) s’ouvre.

     ![nouvelles options de migration de données](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-options.png) 

4. Dans la page **Select sources** (Sélectionner des sources), entrez le nom de l’instance SQL Server contenant le serveur que vous envisagez de migrer. Modifiez les autres valeurs de cette page si nécessaire. Cliquez sur **Connecter**.

     ![nouvelle migration de données, select sources (sélectionner des sources)](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources.png)

5. Dans la partie **Add sources** (Ajouter des sources) de la page **Select sources** (Sélectionner des sources), cochez les cases correspondant aux bases de données dont la compatibilité doit être testée. Cliquez sur **Add**.

     ![nouvelle migration de données, select sources (sélectionner des sources)](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources-add.png)

6. Cliquez sur **Start Assessment** (Démarrer l’évaluation).

     ![nouvelle migration de données, start assessment (démarrer l’évaluation)](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-start-assessment.png)

7. Une fois l’évaluation terminée, déterminez tout d’abord si la base de données est suffisamment compatible pour être migrée. Recherchez la coche dans un cercle vert.

     ![nouvelle migration de données, résultats de l’évaluation de compatibilité](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-compatible.png)

8. Passez en revue les résultats. Les résultats de **parité des fonctionnalités de SQL Server** affichés sont les résultats par défaut à passer en revue. Examinez en particulier les informations sur les fonctionnalités non prises en charge et partiellement prises en charge ainsi que les informations fournies sur les actions recommandées. 

     ![nouvelle migration de données, évaluation de la parité](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-parity.png)

9. Examinez les **problèmes de compatibilité** en cliquant sur cette option dans l’angle supérieur gauche. Examinez en particulier les informations sur les bloqueurs de migration, les changements de comportements et les fonctionnalités dépréciées pour chaque niveau de compatibilité. Pour la base de données AdventureWorks2008R2, passez en revue les modifications apportées à la recherche en texte intégral depuis SQL Server 2008 et les modifications apportées à SERVERPROPERTY('LCID') depuis SQL Server 2000. Pour plus de détails sur ces modifications, des liens vers d’autres d’informations sont fournis. Un grand nombre d’options de recherche et de paramètres de recherche en texte intégral ont changé 

   > [!IMPORTANT] 
   > Après avoir migré votre base de données vers Azure SQL Database, vous pouvez choisir d’utiliser la base de données avec son niveau de compatibilité actuel (niveau 100 pour la base de données AdventureWorks2008R2) ou à un niveau supérieur. Pour plus d’informations sur les implications et les options du fonctionnement d’une base de données à un niveau de compatibilité spécifique, consultez [Niveau de compatibilité ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consultez également [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) pour plus d’informations sur des paramètres supplémentaires au niveau de la base de données relatifs aux niveaux de compatibilité.
   >

10. Vous pouvez éventuellement cliquer sur **Export report** (Exporter le rapport) pour enregistrer le rapport sous forme de fichier JSON.
11. Fermez Data Migration Assistant.

## <a name="export-to-bacpac-file"></a>Exporter vers un fichier BACPAC 

Un fichier BACPAC est un fichier ZIP avec une extension de fichier BACPAC contenant les métadonnées et les données à partir d’une base de données SQL Server. Un fichier BACPAC peut être stocké dans le stockage Blob Azure ou dans le stockage local pour l’archivage ou pour la migration, par exemple de SQL Server vers Azure SQL Database. Pour qu’une exportation soit cohérente au niveau transactionnel, vous devez vérifier qu’aucune activité d’écriture ne se produit lors de l’exportation.

Effectuez les étapes suivantes pour utiliser l’utilitaire en ligne de commande SQLPackage afin d’exporter la base de données AdventureWorks2008R2 vers le stockage local.

1. Ouvrez une invite de commandes Windows et remplacez le répertoire par un dossier dans lequel vous avez la version **130** de SQLPackage, par exemple **C:\Program Files (x86)\Microsoft SQL Server\130\DAC\bin**.

2. Exécutez la commande SQLPackage suivante à l’invite de commandes pour exporter la base de données **AdventureWorks2008R2** de **localhost** vers **AdventureWorks2008R2.bacpac**. Modifiez les valeurs nécessaires en fonction de votre environnement.

    ```SQLPackage
    sqlpackage.exe /Action:Export /ssn:localhost /sdn:AdventureWorks2008R2 /tf:AdventureWorks2008R2.bacpac
    ```

    ![sqlpackage, exportation](./media/sql-database-migrate-your-sql-server-database/sqlpackage-export.png)

Une fois l’exécution terminée, le fichier BACPAC généré est stocké dans le répertoire où se trouve l’exécutable sqlpackage. Dans cet exemple, C:\Program Files (x86)\Microsoft SQL Server\130\DAC\bin. 

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/). Une ouverture de session à partir de l’ordinateur depuis lequel vous exécutez l’utilitaire en ligne de commande SQLPackage facilite la création de la règle de pare-feu à l’étape 5.

## <a name="create-a-sql-server-logical-server"></a>Créer un serveur logique SQL Server

Un [serveur logique SQL Server](sql-database-features.md) fait office de point d’administration central pour plusieurs bases de données. Effectuez les étapes suivantes pour créer un serveur logique SQL Server pour contenir la base de données OLTP AdventureWorks SQL Server migrée. 

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

2. Tapez **sql server** dans la fenêtre de recherche de la page **Nouveau**, puis sélectionnez **SQL database (logical server)** (Base de données SQL [serveur logique]) dans la liste filtrée.

    ![sélectionner un serveur logique](./media/sql-database-migrate-your-sql-server-database/logical-server.png)

3. Dans la page **Tout**, cliquez sur **Serveur SQL Server (serveur logique)**, puis sur **Créer** dans la page **Serveur SQL Server (serveur logique)**.

    ![créer un serveur logique](./media/sql-database-migrate-your-sql-server-database/logical-server-create.png)

4. Remplissez le formulaire de serveur SQL Server (serveur logique) avec les informations suivantes, comme indiqué dans l’illustration précédente :     

   | Paramètre       | Valeur suggérée | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nom du serveur** | Entrez un nom globalement unique | Pour les noms de serveur valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). | 
   | **Connexion d’administrateur du serveur** | Entrez un nom valide | Pour les noms de connexion valides, consultez [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données). |
   | **Mot de passe** | Entrez un mot de passe valide | Votre mot de passe doit comporter au moins 8 caractères et contenir des caractères appartenant à trois des catégories suivantes : caractères en majuscules, caractères en minuscules, chiffres et caractères non alphanumériques. |
   | **Abonnement** | Sélectionner un abonnement | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources** | Choisissez ou créez un groupe de ressources, tel que **myResourceGroup** |  Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Emplacement** | Entrez un emplacement valide pour le nouveau serveur | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |

   ![formulaire de création de serveur logique rempli](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

5. Cliquez sur **Créer** pour approvisionner le serveur logique. L’approvisionnement prend quelques minutes. 

> [!IMPORTANT]
> Mémorisez le nom du serveur, le nom de connexion de l’administrateur du serveur et le mot de passe. Vous aurez besoin de ces valeurs plus loin dans ce didacticiel.
>

## <a name="create-a-server-level-firewall-rule"></a>Créer une règle de pare-feu au niveau du serveur

Le service SQL Database crée un [pare-feu au niveau du serveur](sql-database-firewall-configure.md) qui empêche les applications et les outils externes de se connecter au serveur ou à toute base de données sur le serveur, sauf si une règle de pare-feu est créée pour ouvrir le pare-feu à des adresses IP spécifiques. Effectuez les étapes suivantes pour créer une règle de pare-feu au niveau du serveur SQL Database pour l’adresse IP de l’ordinateur à partir duquel vous exécutez l’utilitaire en ligne de commande SQLPackage. Cela permet à SQLPackage de se connecter au serveur logique SQL Server via le pare-feu Azure SQL Database. 

1. Cliquez sur **Toutes les ressources** dans le menu de gauche et sur le nouveau serveur dans la page **Toutes les ressources**. La page de présentation correspondant à votre serveur s’ouvre et propose des options pour poursuivre la configuration de la page.

     ![présentation du serveur logique](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

2. Cliquez sur **Pare-feu** dans le menu de gauche sous **Paramètres** dans la page de présentation. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre. 

3. Cliquez sur **Ajouter une adresse IP cliente** dans la barre d’outils pour ajouter l’adresse IP de l’ordinateur que vous utilisez, puis cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour cette adresse IP.

     ![définir la règle de pare-feu de serveur](./media/sql-database-migrate-your-sql-server-database/server-firewall-rule-set.png)

4. Cliquez sur **OK**.

Vous pouvez maintenant vous connecter à toutes les bases de données sur ce serveur à l’aide de SQL Server Management Studio ou de tout autre outil de votre choix à partir de cette adresse IP à l’aide du compte d’administrateur serveur créé précédemment.

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

## <a name="import-a-bacpac-file-to-azure-sql-database"></a>Importer un fichier BACPAC dans Azure SQL Database 

Les dernières versions de l’utilitaire en ligne de commande SQLPackage prennent en charge la création d’une base de données Azure SQL Database à des [niveaux de service et de performances](sql-database-service-tiers.md) spécifiés. Pour obtenir des performances optimales lors de l’importation, sélectionnez des niveaux de service et de performances élevés, puis réduisez-les après l’importation s’ils sont supérieurs à vos besoins immédiats.

Effectuez les étapes suivantes pour utiliser l’utilitaire en ligne de commande SQLPackage afin d’importer la base de données AdventureWorks2008R2 dans Azure SQL Database. Si vous pouvez utiliser SQL Server Management Studio pour cette tâche, SQLPackage est la méthode recommandée pour la plupart des environnements de production afin de bénéficier d’une flexibilité maximale et de performances optimales. Pour en savoir plus, voir [Migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

- Exécutez la commande SQLPackage suivante à l’invite de commandes pour importer la base de données **AdventureWorks2008R2** à partir du stockage local vers le serveur logique Azure SQL que vous avez créé précédemment pour une nouvelle base de données, un niveau de service **Premium**, et un objectif de service **P6**. Remplacez les valeurs entre crochets angulaires par des valeurs appropriées pour votre serveur logique SQL Server, et spécifiez un nom pour la nouvelle base de données (remplacez également les crochets angulaires). Vous pouvez aussi modifier les valeurs de l’édition de base de données et du service objectgive en fonction de votre environnement. Pour les besoins de ce didacticiel, la base de données migrée est nommée **myMigratedDatabase**.

    ```
    SqlPackage.exe /a:import /tcs:"Data Source=<your_server_name>.database.windows.net;Initial Catalog=<your_new_database_name>;User Id=<change_to_your_admin_user_account>;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
    ```

   ![sqlpackage, importation](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> Un serveur logique SQL Server écoute le port 1433. Si vous essayez de vous connecter à un serveur logique SQL Server à partir d’un pare-feu d’entreprise, ce port doit être ouvert dans le pare-feu d’entreprise pour que vous puissiez vous connecter.
>

## <a name="connect-using-sql-server-management-studio-ssms"></a>Se connecter avec SSMS (SQL Server Management Studio)

Utilisez SQL Server Management Studio pour établir une connexion à votre serveur Azure SQL Database et à la base de données nouvellement migrée, nommée **myMigratedDatabase** dans ce didacticiel. Si vous exécutez SQL Server Management Studio sur un ordinateur différent de celui à partir duquel vous avez exécuté SQLPackage, créez une règle de pare-feu pour cet ordinateur en suivant les étapes de la procédure précédente.

1. Ouvrez SQL Server Management Studio.

2. Dans la fenêtre **Se connecter au serveur**, entrez les valeurs suivantes :
   - **Type de serveur** : spécifiez le moteur de base de données
   - **Nom du serveur** : entrez votre nom de serveur complet, par exemple **mynewserver20170403.database.windows.net**
   - **Authentification** : spécifiez l’authentification SQL Server
   - **Connexion** : entrez votre compte d’administrateur de serveur
   - **Mot de passe** : entrez le mot de passe de votre compte d’administrateur de serveur
 
    ![se connecter avec ssms](./media/sql-database-migrate-your-sql-server-database/connect-ssms.png)

3. Cliquez sur **Connecter**. La fenêtre Explorateur d’objets s’ouvre. 

4. Dans l’Explorateur d’objets, développez **Bases de données**, puis **myMigratedDatabase** pour afficher les objets dans l’exemple de base de données.

## <a name="change-database-properties"></a>Modifier les propriétés de base de données

Vous pouvez modifier le niveau de service, le niveau de performances et le niveau de compatibilité à l’aide de SQL Server Management Studio. Pendant la phase d’importation, nous recommandons d’importer dans une base de données de niveau supérieur pour bénéficier de performances optimales, mais que vous faites descendre en puissance une fois l’importation terminée afin d’économiser les coûts en attendant d’être prêt à utiliser activement la base de données importée. La modification du niveau de compatibilité peut offrir de meilleures performances et un accès aux fonctionnalités les plus récentes du service Azure SQL Database. Lorsque vous migrez une base de données ancienne, la compatibilité de celle-ci est maintenue au niveau pris en charge le plus bas compatible avec la base de données en cours d’importation. Pour plus d’informations, voir [Amélioration des performances des requêtes avec le niveau de compatibilité 130 dans Azure SQL Database](sql-database-compatibility-level-query-performance-130.md).

1. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur **myMigratedDatabase**, puis cliquez sur **Nouvelle requête**. Une fenêtre de requête connectée à votre base de données s’ouvre.

2. Exécutez la commande suivante pour affecter au niveau de service la valeur **Standard** et au niveau de performances la valeur **S1**.

    ```
    ALTER DATABASE myMigratedDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

    ![changer le niveau de service](./media/sql-database-migrate-your-sql-server-database/service-tier.png)

3. Exécutez la commande suivante pour remplacer le niveau de compatibilité de la base de données par **130**.

    ```
    ALTER DATABASE myMigratedDatabase  
    SET COMPATIBILITY_LEVEL = 130;
    ```

   ![modifier le niveau de compatibilité](./media/sql-database-migrate-your-sql-server-database/compat-level.png)

## <a name="next-steps"></a>Étapes suivantes 
Dans ce didacticiel, vous avez préparé votre base de données, vous l’avez exportée et importée. Vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparer une base de données dans SQL Server pour la migration vers la base de données SQL Azure
> * Exporter la base de données vers un fichier BACPAC
> * Importer un fichier BACPAC dans une base de données SQL Azure

Passez au didacticiel suivant pour apprendre à sécuriser votre base de données.

> [!div class="nextstepaction"]
> [Sécuriser votre base de données SQL Azure](sql-database-security-tutorial.md).



