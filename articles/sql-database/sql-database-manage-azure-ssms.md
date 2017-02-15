---
title: "Gestion d’une base de données SQL avec SSMS | Documents Microsoft"
description: "Découvrez comment utiliser SQL Server Management Studio pour gérer des serveurs et les bases de données de la base de données SQL."
services: sql-database
documentationcenter: .net
author: stevestein
manager: jhubbard
editor: tysonn
ms.assetid: da6f3608-5993-4134-a497-ff2811e9f31f
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b4dc2137ce42e3ed8e85d57dacc1acecc6de243e


---
# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio
> [!div class="op_single_selector"]
> * [Portail Azure](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

Vous pouvez utiliser SQL Server Management Studio (SSMS) pour administrer les serveurs et les bases de données de Base de données SQL Azure. Cette rubrique présente les tâches courantes effectuées avec SSMS. Avant de démarrer, vous devez déjà disposer d’un serveur et d’une base de données créés dans Base de données SQL Azure. Pour plus d’informations, consultez [Didacticiel sur SQL Database : Créer une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md) et [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md).

Nous vous recommandons d’utiliser la dernière version de SSMS, quel que soit l’emplacement choisi pour utiliser la base de données Azure SQL. 

> [!IMPORTANT]
> Utilisez toujours la version la plus récente de SSMS, car l’application est améliorée en permanence pour fonctionner avec les dernières mises à jour d’Azure et de Base de données SQL. Pour obtenir la dernière version, voir [Télécharger SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="create-and-manage-azure-sql-databases"></a>Créer et gérer les bases de données Azure SQL
Lorsque vous êtes connecté à la base de données **MASTER** , vous pouvez créer des bases de données sur le serveur, et modifier ou supprimer des bases de données existantes. La procédure ci-dessous décrit comment accomplir plusieurs tâches courantes de gestion de base de données via Management Studio. Pour effectuer ces tâches, vérifiez que vous êtes connecté à la base de données **principale** avec la connexion principale de niveau serveur que vous avez créée lors de la configuration de votre serveur.

Pour ouvrir une fenêtre de requête dans Management Studio, ouvrez le dossier Bases de données, développez le dossier **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.

* Utilisez l’instruction **CREATE DATABASE** pour créer une base de données. Pour plus d'informations, consultez la rubrique [CREATE DATABASE (Base de données SQL)](https://msdn.microsoft.com/library/dn268335.aspx). L’instruction suivante crée une base de données appelée **maDBTest** , et spécifie qu’il s’agit d’une base de données Standard S0 Edition d’une taille maximale de 250 Go.
  
      CREATE DATABASE myTestDB
      (EDITION='Standard',
       SERVICE_OBJECTIVE='S0');

Cliquez sur **Exécuter** pour exécuter la requête.

* Utilisez l’instruction **ALTER DATABASE** pour modifier une base de données existante, par exemple pour modifier le nom ou l’édition de la base de données. Pour plus d'informations, consultez [ALTER DATABASE (Base de données SQL)](https://msdn.microsoft.com/library/ms174269.aspx). L’instruction suivante modifie la base de données que vous avez créée à l’étape précédente pour changer l’édition en Standard S1.
  
      ALTER DATABASE myTestDB
      MODIFY
      (SERVICE_OBJECTIVE='S1');
* Utilisez l’instruction **DROP DATABASE** pour supprimer une base de données existante. Pour plus d’informations, consultez [DROP DATABASE (Base de données SQL)](https://msdn.microsoft.com/library/ms178613.aspx). L’instruction suivante supprime la base de données **maDBTest** , mais ne la supprimez pas maintenant car vous en aurez besoin pour créer des connexions à la prochaine étape.
  
      DROP DATABASE myTestBase;
* La base de données principale dispose d’une vue **sys.databases** vous permettant d’afficher des informations sur toutes les bases de données. Pour afficher toutes les bases de données existantes, exécutez l’instruction suivante :
  
      SELECT * FROM sys.databases;
* Dans la base de données SQL, l’instruction **USE** ne permet pas de passer d’une base de données à une autre. À la place, vous devez créer une connexion directe à la base de données cible.

> [!NOTE]
> Plusieurs des instructions Transact-SQL permettant de créer ou de modifier une base de données doivent être exécutées au sein de leur propre lot et ne peuvent pas être regroupées avec d’autres instructions Transact-SQL. Pour plus d’informations, voir les informations spécifiques de l’instruction.
> 
> 

## <a name="create-and-manage-logins"></a>création et gestion de connexions
La base de données **MASTER** contient les ID de connexion et répertorie ceux qui sont autorisés à créer des bases de données ou d’autres ID de connexion. Gérez les connexions en vous connectant à la base de données **principale** avec la connexion principale de niveau serveur que vous avez créée lors de la configuration de votre serveur. Vous pouvez utiliser les instructions **CREATE LOGIN**, **ALTER LOGIN** et **DROP LOGIN** pour exécuter des requêtes sur la base de données Master qui gère les connexions sur l’ensemble du serveur. Pour plus d'informations, consultez [Gestion des bases de données et des connexions dans la base de données SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx). 

* Utilisez l’instruction **CREATE LOGIN** pour créer une connexion de niveau serveur. Pour plus d'informations, consultez [CREATE LOGIN (Base de données SQL)](https://msdn.microsoft.com/library/ms189751.aspx). L’instruction suivante crée une connexion intitulée **Connexion1**. Remplacez **motdepasse1** par le mot de passe de votre choix.
  
      CREATE LOGIN login1 WITH password='password1';
* Utilisez l’instruction **CREATE USER** pour octroyer des autorisations au niveau de la base de données. Toutes les connexions doivent être créées dans la base de données **MASTER** . Pour qu’une connexion permette d’accéder à une autre base de données, vous devez lui octroyer des autorisations de niveau de base de données en utilisant l’instruction **CREATE USER** sur cette base de données. Pour plus d'informations, consultez [CREATE USER (Base de données SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 
* Pour fournir à connexion1 des autorisations d’accès à une base de données appelée **maBDTest**, procédez comme suit :
  
  1. Pour actualiser l’Explorateur d’objets et voir la base de données **maBDTest** que vous avez créée, cliquez avec le bouton droit sur le nom du serveur dans l’Explorateur d’objets, puis cliquez sur **Actualiser**.  
     
     Si vous avez fermé la connexion, vous pouvez vous reconnecter en sélectionnant **Connecter l’Explorateur d’objets** dans le menu Fichier.
  2. Cliquez avec le bouton droit sur **maBDTest** et sélectionnez **Nouvelle requête**.
  3. Exécutez l’instruction suivante sur la base de données maBDTest pour créer un utilisateur de base de données nommé **utilisateur_connexion1** correspondant à la connexion de niveau serveur **connexion1**.
     
         CREATE USER login1User FROM LOGIN login1;
* Utilisez la procédure stockée **sp\_addrolemember** pour fournir au compte d’utilisateur le niveau approprié d’autorisations sur la base de données. Pour plus d’informations, consultez [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). L’instruction suivante fournit à **utilisateur_connexion1** des autorisations d’accès en lecture seule à la base de données en ajoutant **utilisateur_connexion1** au rôle **db\_datareader**.
  
      exec sp_addrolemember 'db_datareader', 'login1User';    
* Utilisez l’instruction **ALTER LOGIN** pour modifier une connexion existante, par exemple si vous voulez modifier le mot de passe de la connexion. Pour plus d'informations, consultez [ALTER LOGIN (Base de données SQL)](https://msdn.microsoft.com/library/ms189828.aspx). L’instruction **ALTER LOGIN** doit être exécutée sur la base de données **Master**. Revenez à la fenêtre de requête qui est connectée à cette base de données. L’instruction suivante modifie la connexion **connexion1** pour réinitialiser le mot de passe. Remplacez **nouveauMotPasse** par le mot de passe de votre choix et **ancienMotPasse** par le mot de passe actuel de la connexion.
  
      ALTER LOGIN login1
      WITH PASSWORD = 'newPassword'
      OLD_PASSWORD = 'oldPassword';
* Utilisez l’instruction **DROP LOGIN** pour supprimer une connexion existante. La suppression d’une connexion au niveau du serveur a également pour effet de supprimer tous les comptes d’utilisateur de base de données associés. Pour plus d’informations, consultez [DROP DATABASE (Base de données SQL)](https://msdn.microsoft.com/library/ms178613.aspx). L’instruction **DROP LOGIN** doit être exécutée sur la base de données **Master**. L’instruction suivante supprime la connexion **connexion1** .
  
      DROP LOGIN login1;
* La base de données Master fournit la vue **sys.sql\_logins** qui vous permet d’afficher les connexions. Pour afficher toutes les connexions existantes, exécutez l’instruction suivante :
  
      SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>contrôle de la base de données SQL au moyen de vues de gestion dynamique
La base de données SQL prend en charge plusieurs vues de gestion dynamique vous permettant de surveiller une base de données individuelle. Voici quelques exemples du type de données de surveillance que vous pouvez récupérer au moyen de ces vues. Pour plus d'informations et pour obtenir d'autres exemples d’utilisation, consultez [Surveillance d’une base de données SQL Azure à l’aide de vues de gestion dynamique](https://msdn.microsoft.com/library/azure/ff394114.aspx).

* L’interrogation d’une vue de gestion dynamique nécessite des autorisations **VIEW DATABASE STATE** . Pour octroyer l’autorisation **VIEW DATABASE STATE** à un utilisateur de base de données spécifique, connectez-vous à la base de données, puis exécutez l’instruction suivante sur celle-ci :
  
      GRANT VIEW DATABASE STATE TO login1User;
* Calculez la taille de la base de données au moyen de la vue **sys.dm\_db\_partition\_stats**. La vue **sys.dm\_db\_partition\_stats** renvoie le nombre de pages et de lignes de chaque partition de la base de données, des informations que vous pouvez utiliser pour calculer la taille de la base de données. La requête suivante renvoie la taille de votre base de données en mégaoctets :
  
      SELECT SUM(reserved_page_count)*8.0/1024
      FROM sys.dm_db_partition_stats;   
* Utilisez les vues **sys.dm\_exec\_connections** et **sys.dm\_exec\_sessions** pour extraire les informations concernant les connexions utilisateur actuelles et les tâches internes associées à la base de données. La requête suivante renvoie des informations sur la connexion actuelle :
  
      SELECT
          e.connection_id,
          s.session_id,
          s.login_name,
          s.last_request_end_time,
          s.cpu_time
      FROM
          sys.dm_exec_sessions s
          INNER JOIN sys.dm_exec_connections e
            ON s.session_id = e.session_id;
* Utilisez la vue **sys.dm\_exec\_query\_stats** pour récupérer les statistiques de performance cumulées sur les plans de requête mise en cache. La requête suivante renvoie des informations relatives aux cinq requêtes principales classées sur la base du temps processeur moyen.
  
      SELECT TOP 5 query_stats.query_hash AS "Query Hash",
          SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
          MIN(query_stats.statement_text) AS "Statement Text"
      FROM
          (SELECT QS.*,
          SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
          ((CASE statement_end_offset
              WHEN -1 THEN DATALENGTH(ST.text)
              ELSE QS.statement_end_offset END
                  - QS.statement_start_offset)/2) + 1) AS statement_text
           FROM sys.dm_exec_query_stats AS QS
           CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
      GROUP BY query_stats.query_hash
      ORDER BY 2 DESC;




<!--HONumber=Nov16_HO3-->


