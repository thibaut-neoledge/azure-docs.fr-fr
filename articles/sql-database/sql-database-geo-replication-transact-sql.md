---
title: "Configurer la géoréplication pour Azure SQL Database avec Transact-SQL | Documents Microsoft"
description: "Configurer la géoréplication pour Azure SQL Database à l’aide de Transact-SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d94d89a6-3234-46c5-8279-5eb8daad10ac
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/14/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: dad35a2b3beb2b07d5b12afb8a04ba48f8b8ef7e
ms.contentlocale: fr-fr
ms.lasthandoff: 05/18/2017


---
# <a name="configure-active-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configurer la géoréplication active pour Base de données SQL Azure avec Transact-SQL

Cet article vous montre comment configurer la géoréplication active pour une base de données SQL Azure à l’aide de Transact-SQL.

Pour lancer un basculement avec Transact-SQL, consultez [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

> [!NOTE]
> Lorsque vous utilisez la géoréplication active (bases de données secondaires lisibles) pour la récupération d’urgence, vous devez configurer un groupe de basculement pour toutes les bases de données au sein d’une application afin de permettre un basculement automatique et transparent. Cette fonctionnalité est en préversion. Pour plus d’informations, voir [Groupes de basculement automatique et géoréplication](sql-database-geo-replication-overview.md).
> 
> 

Pour configurer la géoréplication active à l’aide de Transact-SQL, vous devez disposer des éléments suivants :

* Abonnement Azure
* Serveur logique Azure SQL Database <MyLocalServer> et base de données SQL <MyDB> (base de données primaire à répliquer)
* Un ou plusieurs serveurs logiques Azure SQL Database <MySecondaryServer(n)> (les serveurs logiques qui seront les serveurs partenaires dans lesquels créer des bases de données secondaires)
* ID de connexion DBManager sur le serveur principal
* Droits db_ownership sur la base de données locale que vous allez géorépliquer
* Rôle DBManager sur les serveurs partenaires sur lesquels vous voulez configurer la géoréplication
* Dernière version de SQL Server Management Studio (SSMS)

> [!IMPORTANT]
> Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="add-secondary-database"></a>Ajout d'une base de données secondaire
Vous pouvez utiliser l’instruction **ALTER DATABASE** pour créer une base de données secondaire géo-répliquée sur un serveur partenaire. Vous exécutez cette instruction sur la base de données master du serveur contenant la base de données à répliquer. La base de données géo-répliquée (« base de données primaire ») aura le même nom que la base de données répliquée et aura, par défaut, le même niveau de service que la base de données primaire. La base de données secondaire peut être accessible en lecture ou non, et il peut s’agir d’une base de données unique ou d’une base de données dans un pool élastique. Pour plus d’informations, consultez [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et [Niveaux de service](sql-database-service-tiers.md).
Une fois la seconde base de données secondaire créée et semée, les données vont commencer une réplication asynchrone depuis la base de données primaire. Les étapes suivantes décrivent comment configurer la géoréplication à l’aide de Management Studio. Vous trouverez les opérations destinées à créer des éléments secondaires avec accès en lecture ou non, soit comme base de données unique, soit comme base de données dans un pool élastique.

> [!NOTE]
> Si une base de données existe sur le serveur partenaire spécifié avec le même nom qu’une base de données primaire, la commande échoue.
> 

### <a name="add-readable-secondary-single-database"></a>Ajouter une base de données secondaire accessible en lecture (base de données unique)
Utilisez les étapes suivantes pour créer une base de données secondaire accessible en lecture en tant que base de données unique.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.
2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction **ALTER DATABASE** suivante pour créer une base de données locale dans une géoréplication primaire avec une base de données secondaire accessible en lecture sur un serveur secondaire.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);
4. Cliquez sur **Exécuter** pour exécuter la requête.

### <a name="add-readable-secondary-elastic-pool"></a>Ajouter une base de données secondaire accessible en lecture (pool élastique)
Utilisez les étapes suivantes pour créer une base de données secondaire accessible en lecture dans un pool élastique.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.
2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction **ALTER DATABASE** suivante pour créer une base de données locale dans une géoréplication primaire avec une base de données secondaire accessible en lecture sur un serveur secondaire dans un pool élastique.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));
4. Cliquez sur **Exécuter** pour exécuter la requête.

## <a name="remove-secondary-database"></a>Supprimer une base de données secondaire
Vous pouvez utiliser l’instruction **ALTER DATABASE** pour arrêter définitivement le partenariat de réplication entre une base de données secondaire et sa base de données primaire. Cette instruction est exécutée sur la base de données master sur lequel réside la base de données primaire. Après la fin de la relation, la base de données secondaire devient une base de données accessible en lecture-écriture. Si la connectivité à la base de données secondaire est interrompue, cette commande réussit mais la base de données secondaire devient accessible en lecture-écriture une fois la connectivité rétablie. Pour plus d’informations, consultez [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et [Niveaux de service](sql-database-service-tiers.md).

Suivez les étapes suivantes pour supprimer la base de données secondaire géorépliquée d’un partenariat de géoréplication.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.
2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction **ALTER DATABASE** suivant pour supprimer une base de données secondaire géo-répliquée.
   
        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;
4. Cliquez sur **Exécuter** pour exécuter la requête.

## <a name="monitor-active-geo-replication-configuration-and-health"></a>Surveillance de la configuration et de l’état de la géoréplication active

Les tâches de surveillance incluent la surveillance de la configuration de géo-réplication et la surveillance de l’état de réplication de données.  Vous pouvez utiliser la vue de gestion dynamique **sys.dm_geo_replication_links** dans la base de données Master pour renvoyer des informations sur tous les liens de réplication de chaque base de données sur le serveur logique de base de données SQL Azure. Cette vue contient une ligne pour chacun des liens de réplication entre les bases de données primaires et secondaires. Vous pouvez utiliser la vue de gestion dynamique **sys.dm_replication_link_status** pour renvoyer une ligne pour chaque base de données SQL Azure actuellement engagée dans un lien de réplication. Cela inclut les bases de données primaires et secondaires. S’il existe plusieurs liens de réplication continus pour une base de données primaire donnée, cette table contient une ligne pour chacune des relations. La vue est créée dans toutes les bases de données, y compris la logique principale. Toutefois, l’interrogation de cette vue dans la logique principale renvoie un jeu vide. Vous pouvez utiliser la vue de gestion dynamique **sys.dm_operation_status** pour afficher l’état de toutes les opérations de base de données, et notamment l’état des liens de réplication. Pour plus d’informations, consultez [sys.geo_replication_links (Base de données SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (Base de données SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx) et [sys.dm_operation_status (Base de données SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Utilisez les étapes suivantes pour surveiller un partenariat de géoréplication active.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.
2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction suivante pour afficher toutes les bases de données avec des liens de géoréplication.
   
        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].dm_geo_replication_links;
4. Cliquez sur **Exécuter** pour exécuter la requête.
5. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **MyDB**, puis cliquez sur **Nouvelle requête**.
6. Utilisez l’instruction suivante pour afficher les retards de réplication et l’heure de la dernière réplication des bases de données secondaires de MyDB.
   
        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status
7. Cliquez sur **Exécuter** pour exécuter la requête.
8. Utilisez l’instruction suivante pour afficher les opérations de géo-réplication plus récentes associées à la base de données MyDB.
   
        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC
9. Cliquez sur **Exécuter** pour exécuter la requête.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la géoréplication active, voir [Géoréplication active](sql-database-geo-replication-overview.md).
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)


