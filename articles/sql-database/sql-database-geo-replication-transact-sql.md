---
title: "Configurer la géoréplication pour Base de données SQL Azure avec Transact-SQL | Documents Microsoft"
description: "Configurer la géoréplication pour Base de données SQL Azure avec Transact-SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d94d89a6-3234-46c5-8279-5eb8daad10ac
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 702a01f7ea56e8af6286149bcb42590294cb618b


---
# <a name="configure-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configurer la géoréplication pour Base de données SQL Azure avec Transact-SQL
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-database-geo-replication-overview.md)
> * [Portail Azure](sql-database-geo-replication-portal.md)
> * [PowerShell](sql-database-geo-replication-powershell.md)
> * [T-SQL](sql-database-geo-replication-transact-sql.md)
> 
> 

Cet article vous montre comment configurer la géo-réplication active pour une base de données SQL Azure à l’aide de Transact-SQL.

Pour lancer un basculement avec Transact-SQL, consultez [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

> [!NOTE]
> La géo-réplication active (bases de données secondaires accessibles en lecture) est désormais disponible pour toutes les bases de données de tous les niveaux de service. En avril 2017 sera retiré le type secondaire non accessible en lecture et les bases de données non accessibles en lecture deviendront automatiquement des bases de données secondaires accessibles en lecture.
> 
> 

Pour configurer la géoréplication active à l’aide de Transact-SQL, vous devez disposer des éléments suivants :

* Un abonnement Azure.
* Un serveur de base de données SQL Azure <MyLocalServer> et une base de données SQL <MyDB> : la base de données primaire que vous souhaitez répliquer.
* Un ou plusieurs serveurs logiques de bases de données SQL Azure <MySecondaryServer(n)> - Les serveurs logiques qui seront serveurs partenaires dans lequel vous créerez des bases de données secondaires.
* Une connexion qui est DBManager sur le serveur principal, a la propriété db_ownership de la base de données locale que vous allez géo-répliquer et est DBManager sur le ou les serveurs partenaires sur lesquels vous allez configurer la géoréplication.
* SQL Server Management Studio (SSMS)

> [!IMPORTANT]
> Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="add-secondary-database"></a>Ajout d'une base de données secondaire
Vous pouvez utiliser l’instruction **ALTER DATABASE** pour créer une base de données secondaire géo-répliquée sur un serveur partenaire. Vous exécutez cette instruction sur la base de données master du serveur contenant la base de données à répliquer. La base de données géo-répliquée (« base de données primaire ») aura le même nom que la base de données répliquée et aura, par défaut, le même niveau de service que la base de données primaire. La base de données secondaire peut être accessible en lecture ou non et il peut s’agir d’une base de données unique ou une base de données élastique. Pour plus d’informations, consultez [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et [Niveaux de service](sql-database-service-tiers.md).
Une fois la seconde base de données secondaire créée et semée, les données vont commencer une réplication asynchrone depuis la base de données primaire. Les étapes suivantes décrivent comment configurer la géoréplication à l’aide de Management Studio. Vous trouverez les opérations destinées à créer des éléments secondaires avec accès en lecture ou non, soit avec une base de données unique, soit avec une base de données élastique.

> [!NOTE]
> Si une base de données existe sur le serveur partenaire spécifié avec le même nom qu’une base de données primaire, la commande échoue.
> 
> 

### <a name="add-non-readable-secondary-single-database"></a>Ajoutez une base de données non accessible en lecture secondaire non lisible (base de données unique)
Utilisez les étapes suivantes pour créer une base de données non lisible en tant que base de données unique.

1. Vous devez disposer de la version 13.0.600.65 ou d’une version ultérieure de SQL Server Management Studio.
   
   > [!IMPORTANT]
   > Téléchargez la [dernière](https://msdn.microsoft.com/library/mt238290.aspx) version de SQL Server Management Studio. Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour publiées sur le portail Azure.
   > 
   > 
2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction **ALTER DATABASE** suivante pour créer une base de données locale dans une géoréplication primaire avec une base de données secondaire non accessible en lecture sur MySecondaryServer1, où MySecondaryServer1 est le nom de serveur convivial.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);
4. Cliquez sur **Exécuter** pour exécuter la requête.

### <a name="add-readable-secondary-single-database"></a>Ajouter une base de données secondaire accessible en lecture (base de données unique)
Utilisez les étapes suivantes pour créer une base de données secondaire accessible en lecture en tant que base de données unique.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.
2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction **ALTER DATABASE** suivante pour fabriquer une base de données locale dans une géoréplication primaire avec une base de données secondaire accessible en lecture sur un serveur secondaire.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);
4. Cliquez sur **Exécuter** pour exécuter la requête.

### <a name="add-non-readable-secondary-elastic-database"></a>Ajouter une base de données secondaire non accessible en lecture (base de données élastique)
Utilisez les étapes suivantes pour créer une base de données secondaire non accessible en lecture en tant que base de données élastique.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.
2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction **ALTER DATABASE** suivante pour créer une base de données locale dans une géoréplication primaire avec une base de données secondaire non accessible en lecture sur un serveur secondaire dans un pool élastique.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));
4. Cliquez sur **Exécuter** pour exécuter la requête.

### <a name="add-readable-secondary-elastic-database"></a>Ajouter une base de données secondaire accessible en lecture (base de données élastique)
Utilisez les étapes suivantes pour créer une base de données secondaire accessible en lecture en tant que base de données élastique.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.
2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction **ALTER DATABASE** suivante pour créer une base de données locale dans une géoréplication primaire avec une base de données secondaire accessible en lecture sur un serveur secondaire dans un pool élastique.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));
4. Cliquez sur **Exécuter** pour exécuter la requête.

## <a name="remove-secondary-database"></a>Supprimer une base de données secondaire
Vous pouvez utiliser l’instruction **ALTER DATABASE** pour arrêter définitivement le partenariat de réplication entre une base de données secondaire et sa base de données primaire. Cette instruction est exécutée sur la base de données master sur lequel réside la base de données primaire. Après la fin de la relation, la base de données secondaire devient une base de données accessible en lecture-écriture. Si la connectivité à la base de données secondaire est interrompue, cette commande réussit mais la base de données secondaire devient accessible en lecture-écriture une fois la connectivité rétablie. Pour plus d’informations, consultez [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et [Niveaux de service](sql-database-service-tiers.md).

Utilisez les étapes suivantes pour supprimer la base de données secondaire répliquée d’un partenariat de géoréplication.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.
2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction **ALTER DATABASE** suivant pour supprimer une base de données secondaire géo-répliquée.
   
        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;
4. Cliquez sur **Exécuter** pour exécuter la requête.

## <a name="monitor-geo-replication-configuration-and-health"></a>Surveillance de la configuration et de l’état de géoréplication
Les tâches de surveillance incluent la surveillance de la configuration de géoréplication et la surveillance de l’état de réplication de données.  Vous pouvez utiliser la vue de gestion dynamique **sys.dm_geo_replication_links** dans la base de données Master pour renvoyer des informations sur tous les liens de réplication de chaque base de données sur le serveur logique de base de données SQL Azure. Cette vue contient une ligne pour chacun des liens de réplication entre les bases de données primaires et secondaires. Vous pouvez utiliser la vue de gestion dynamique **sys.dm_replication_link_status** pour renvoyer une ligne pour chaque base de données SQL Azure actuellement engagée dans un lien de réplication. Cela inclut les bases de données primaires et secondaires. S’il existe plusieurs liens de réplication continus pour une base de données primaire donnée, cette table contient une ligne pour chacune des relations. La vue est créée dans toutes les bases de données, y compris la logique principale. Toutefois, l’interrogation de cette vue dans la logique principale renvoie un jeu vide. Vous pouvez utiliser la vue de gestion dynamique **sys.dm_operation_status** pour afficher l’état de toutes les opérations de base de données, et notamment l’état des liens de réplication. Pour plus d’informations, consultez [sys.geo_replication_links (Base de données SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (Base de données SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx) et [sys.dm_operation_status (Base de données SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Utilisez les étapes suivantes pour surveiller un partenariat de géoréplication.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.
2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction suivante pour afficher toutes les bases de données avec des liens de géoréplication.
   
        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;
4. Cliquez sur **Exécuter** pour exécuter la requête.
5. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **MyDB**, puis cliquez sur **Nouvelle requête**.
6. Utilisez l’instruction suivante pour afficher les retards de réplication et l’heure de la dernière réplication des bases de données secondaires de MyDB.
   
        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status
7. Cliquez sur **Exécuter** pour exécuter la requête.
8. Utilisez l’instruction suivante pour afficher les opérations de géo-réplication plus récentes associées à la base de données MyDB.
   
        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC
9. Cliquez sur **Exécuter** pour exécuter la requête.

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>Mettre à niveau une base de données secondaire non accessible en lecture en une base de données accessible en lecture
En avril 2017 sera retiré le type secondaire non accessible en lecture et les bases de données non accessibles en lecture deviendront automatiquement des bases de données secondaires accessibles en lecture. Si vous utilisez des bases de données secondaires non accessibles en lecture et souhaitez les rendre accessibles en lecture, vous pouvez utiliser les étapes suivantes pour chaque base de données secondaire.

> [!IMPORTANT]
> Il n’existe aucune méthode en libre-service de mise à niveau in situ d’une base de données secondaire non accessible en lecture en une base de données accessible en lecture. Si vous supprimez votre unique base de données secondaire, la base de données primaire restera sans protection jusqu'à ce que la nouvelle base de données secondaire soit entièrement synchronisée. Si le contrat de mise à niveau de service (SLA) de votre application nécessite la protection permanente de la base de données primaire, vous pouvez créer une base de données parallèle sur un autre serveur avant d’appliquer la procédure de mise à niveau ci-dessus. Notez que chaque base de données primaire peut avoir jusqu'à 4 bases de données secondaires.
> 
> 

1. Connectez-vous d’abord au serveur *secondaire* et supprimez la base de données secondaire non accessible en lecture :  
   
        DROP DATABASE <MyNonReadableSecondaryDB>;
2. Connectez-vous ensuite au serveur *principal* serveur et ajoutez une nouvelle base de données secondaire accessible en lecture
   
        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la géoréplication active, consultez [Géoréplication active](sql-database-geo-replication-overview.md)
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)




<!--HONumber=Nov16_HO3-->


