<properties
    pageTitle="Configurer la géo-réplication pour la base de données SQL Azure avec Transact-SQL | Microsoft Azure"
    description="Configurer la géo-réplication pour Base de données SQL Azure avec Transact-SQL"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="01/25/2015"
    ms.author="carlrab"/>

# Configurer la géo-réplication pour Base de données SQL Azure avec Transact-SQL



> [AZURE.SELECTOR]
- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


Cet article vous montre comment configurer la géo-réplication pour une base de données SQL Azure à l’aide de Transact-SQL.


La géo-réplication permet de créer jusqu’4 réplicas de bases de données (secondaires) dans différents centres de données (régions). Les bases de données secondaires sont disponibles en cas d’indisponibilité d’un centre de données ou l’incapacité à se connecter à la base de données primaire.

La géo-réplication est uniquement disponible pour les bases de données Standard et Premium.

Les bases de données standard peuvent avoir un serveur secondaire non accessible en lecture et doivent utiliser la région recommandée. Les bases de données Premium peuvent générer jusqu’à quatre réplicas secondaires dans des régions disponibles.


Pour configurer la géo-réplication, vous devez disposer des éléments suivants :

- Abonnement Azure : si vous ne disposez pas d’un abonnement Azure, cliquez simplement sur **VERSION D’ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Un serveur de base de données SQL Azure logique <MyLocalServer> et une base de données SQL <MyDB>. La base de données primaire que vous souhaitez répliquer vers une autre région géographique.
- Un ou plusieurs serveurs logiques de bases de données SQL Azure < MySecondaryServer(n) > - Les serveurs logiques qui seront serveurs partenaires dans lequel vous créerez des bases de données secondaires de géo-réplication.
- Une connexion d’accès DBManager sur le serveur principal, ont la propriété db\_ownership de la base de données locale que vous allez géo-répliquer, et est DBManager sur le ou les serveurs partenaires auxquels vous allez configurer la géo-réplication.
- La version la plus récente de SQL Server Management Studio. Pour obtenir la version la plus récente de SQL Server Management Studio (SSMS), accédez à [Télécharger SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Pour plus d’informations sur l’utilisation de SQL Server Management Studio dans la gestion des serveurs logiques et des bases de données SQL Azure, consultez [Gestion de base de données SQL à l’aide de SQL Server Management Studio](sql-database-manage-azure-ssms.md)

## Ajout d'une base de données secondaire

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour créer une base de données secondaire géo-répliquée sur un serveur partenaire. Vous exécutez cette instruction sur la base de données master du serveur contenant la base de données à répliquer. La base de données géo-répliquée (« base de données primaire ») aura le même nom que la base de données répliquée et aura, par défaut, le même niveau de service que la base de données primaire. La base de données secondaire peut être accessible en lecture ou non et il peut s’agir d’une base de données unique ou une base de données élastique. Pour plus d’informations, voir [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et [Niveaux de service](sql-database-service-tiers.md). Une fois la seconde base de données secondaire créée et semée, les données vont commencer une réplication asynchrone depuis la base de données primaire. Les étapes suivantes décrivent comment configurer la géo-réplication à l’aide de Management Studio. Vous trouverez les opérations destinées à créer des éléments secondaires avec accès en lecture ou non, soit avec une base de données unique, soit avec une base de données élastique.

> [AZURE.NOTE] Si la base de données secondaire existe sur le serveur partenaire spécifié (par exemple, parce qu’il existe actuellement une relation de géo-réplication ou existait déjà, la commande échoue.


### Ajoutez une base de données non accessible en lecture secondaire non lisible (base de données unique)

Utilisez les étapes suivantes pour créer une base de données non lisible en tant que base de données unique.

1. Vous devez disposer de la version 13.0.600.65 ou d’une version ultérieure de SQL Server Management Studio.

 	 >[AZURE.IMPORTANT] Téléchargez la [dernière](https://msdn.microsoft.com/library/mt238290.aspx) version de SQL Server Management Studio. Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour publiées sur le portail Azure.


2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour créer une base de données locale dans une géo-réplication primaire avec une base de données secondaire non accessible en lecture sur MySecondaryServer1, où MySecondaryServer1 est le nom de serveur convivial.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Cliquez sur **Exécuter** pour exécuter la requête.


### Ajouter une base de données secondaire accessible en lecture (base de données unique)
Utilisez les étapes suivantes pour créer une base de données secondaire accessible en lecture en tant que base de données unique.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.

2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction suivante **ALTER DATABASE** pour fabriquer une base de données locale dans une géo-réplication primaire avec une base de données secondaire accessible en lecture sur un serveur secondaire.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Cliquez sur **Exécuter** pour exécuter la requête.



### Ajouter une base de données secondaire non accessible en lecture (base de données élastique)
Utilisez les étapes suivantes pour créer une base de données secondaire non accessible en lecture en tant que base de données élastique.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.

2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour créer une base de données locale dans une géo-réplication primaire avec une base de données secondaire non accessible en lecture sur un serveur secondaire dans un pool élastique.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. Cliquez sur **Exécuter** pour exécuter la requête.



### Ajouter une base de données secondaire accessible en lecture (base de données élastique)
Utilisez les étapes suivantes pour créer une base de données secondaire accessible en lecture en tant que base de données élastique.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.

2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour créer une base de données locale dans une géo-réplication primaire avec une base de données secondaire accessible en lecture sur un serveur secondaire dans un pool élastique.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. Cliquez sur **Exécuter** pour exécuter la requête.



## Supprimer une base de données secondaire

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour arrêter définitivement le partenariat de réplication entre une base de données secondaire et sa base de données primaire. Cette instruction est exécutée sur la base de données master sur lequel réside la base de données primaire. Après la fin de la relation, la base de données secondaire devient une base de données accessible en lecture-écriture. Si la connectivité à la base de données secondaire est interrompue, cette commande réussit mais la base de données secondaire devient accessible en lecture-écriture une fois la connectivité rétablie. Pour plus d’informations, voir [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et [Niveaux de service](sql-database-service-tiers.md).

Utilisez les étapes suivantes pour supprimer la base de données secondaire répliquée d’un partenariat de géo-réplication.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.

2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivant pour supprimer une base de données secondaire géo-répliquée.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Cliquez sur **Exécuter** pour exécuter la requête.


## Initier un basculement planifié en assurant la promotion d’une base de données secondaire, pour qu’il devienne le nouveau réplica principal

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour promouvoir une base de données secondaire afin qu’elle devienne la nouvelle base de données primaire de manière planifiée, et rétrogradant l’élément primaire existant pour qu’elle devienne secondaire. Cette instruction est exécutée sur la base de données master sur le serveur logique de base de données SQL Azure sur lequel réside la base secondaire géo-répliquée promue. Cette fonctionnalité est conçue pour le basculement planifié, comme pendant les exercices de récupération d’urgence et nécessite que la base de données primaire soit disponible.

La commande exécute le flux de travail suivant :

1. Bascule provisoirement la réplication en mode synchrone, ce qui fait que toutes les transactions en attente doivent être vidées vers le serveur secondaire et toutes les nouvelles transactions bloquées ;

2. Inverse les rôles des deux bases de données du partenariat géo-réplication.

Cette séquence garantit qu’aucune perte de données n’aura lieu. Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (de l’ordre de 0 à 25 secondes) pendant que les rôles sont activés. Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales. Pour plus d’informations, voir [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et [Niveaux de service](sql-database-service-tiers.md).


> [AZURE.NOTE] Si la base de données primaire est indisponible lorsque la commande est émise, la commande échoue avec le message d’erreur indiquant que le serveur principal n’est pas disponible. Dans de rares cas, il est possible que l’opération ne puisse pas s’achever et semble bloquée. Dans ce cas, l’utilisateur peut exécuter la commande de basculement forcé et accepter la perte de données.

Utilisez les étapes suivantes pour initier un basculement planifié.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure dans lesquels réside une base de données secondaire répliquée.

2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour créer une base de données géo-répliquée dans une géo-réplication primaire avec une base de données secondaire accessible en lecture sur <MySecondaryServer4> en <ElasticPool2>.

        ALTER DATABASE <MyDB> FAILOVER;

4. Cliquez sur **Exécuter** pour exécuter la requête.



## Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour promouvoir une base de données secondaire afin qu’elle devienne la nouvelle base de données primaire de façon non planifiée, en forçant la rétrogradation de l’élément primaire existant pour qu’elle devienne secondaire dans le cas où la base de données n’est plus disponible. Cette instruction est exécutée sur la base de données master sur le serveur logique de base de données SQL Azure sur lequel réside la base secondaire géo-répliquée promue.

Cette fonctionnalité est conçue pour la récupération d’urgence lorsque la restauration de la disponibilité de la base de données est essentielle et une perte de données est acceptable. Lorsque le basculement forcé est appelé, la base de données secondaire devient la base de données primaire immédiatement et commence à accepter des transactions d’écriture. Dès que la base de données primaire d’origine est en mesure de se reconnecter à cette base de données primaire, une sauvegarde incrémentielle s’effectue sur la base de données primaire d’origine et l’ancienne base de données primaire est transformée en base de données secondaire de la nouvelle base de données primaire ; par la suite, il s’agit d’un simple réplica de la nouvelle base de données primaire.

Toutefois, étant donné que la limite de restauration n’est pas prise en charge sur les bases de données secondaires, si l’utilisateur souhaite récupérer les données validées dans l’ancienne base de données primaire qui n’ont pas été répliquées dans la nouvelle base de données primaire avant le basculement forcé, l’utilisateur devra faire appel à l’assistance technique pour récupérer les données perdues.

> [AZURE.NOTE] Si la commande est émise lorsque les bases de données primaire et secondaire sont en ligne, l’ancienne base de données primaire deviendra la nouvelle base de données secondaire, mais la synchronisation des données n’aura pas lieu. Des pertes de données peuvent se produire.


Si la base de données primaire comporte plusieurs bases de données secondaires, la commande réussira uniquement sur le serveur secondaire sur lequel la commande a été exécutée. Toutefois, les autres éléments secondaires ne sauront pas que le basculement forcé s’est produit. L’utilisateur devra réparer manuellement cette configuration à l’aide d’une API « supprimer la base de données secondaire », puis reconfigurer la géo-réplication sur ces éléments secondaires supplémentaires.

Utilisez les étapes suivantes pour forcer la suppression de la base de données secondaire géo-répliquée d’un partenariat de géo-réplication.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure dans lesquels réside une base de données secondaire répliquée.

2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour créer <MyLocalDB> dans une géo-réplication primaire avec une base de données secondaire accessible en lecture sur <MySecondaryServer4> en <ElasticPool2>.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Cliquez sur **Exécuter** pour exécuter la requête.

## Surveillance de la configuration et de l’état de géo-réplication

Les tâches de surveillance incluent la surveillance de la configuration de géo-réplication et la surveillance de l’état de réplication de données. Vous pouvez utiliser la vue de gestion dynamique **sys.dm\_geo\_replication\_links** dans la base de données master pour retourner des informations sur tous les liens de réplication de sortie pour chaque base de données sur le serveur logique de base de données SQL Azure. Cette vue contient une ligne pour chacun des liens de réplication entre les bases de données primaires et secondaires. Vous pouvez utiliser la vue de gestion dynamique **sys.dm\_replication\_status** pour retourner une ligne pour chaque base de données SQL Azure actuellement engagée dans un lien de réplication. Cela inclut les bases de données primaires et secondaires. S’il existe plusieurs liens de réplication continus pour une base de données primaire donnée, cette table contient une ligne pour chacune des relations. La vue est créée dans toutes les bases de données, y compris la logique principale. Toutefois, l’interrogation de cette vue dans la logique principale renvoie un jeu vide. Vous pouvez utiliser la vue de gestion dynamique **sys.dm\_operation\_status** pour afficher l’état de toutes les opérations de base de données, et notamment de l’état des liens de réplication. Pour plus d’informations, consultez [sys.geo\_replication\_links (Base de données SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm\_geo\_replication\_link\_status (Base de données SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx) et [sys.dm\_operation\_status (Base de données de SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Utilisez les étapes suivantes pour surveiller un partenariat de géo-réplication.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure.

2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction suivante pour afficher toutes les bases de données avec des liens de géo-réplication.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Cliquez sur **Exécuter** pour exécuter la requête.
5. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **MyDB**, puis cliquez sur **Nouvelle requête**.
6. Utilisez l’instruction suivante pour afficher les retards de réplication et l’heure de la dernière réplication des bases de données secondaires de MyDB.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Cliquez sur **Exécuter** pour exécuter la requête.
8. Utilisez l’instruction suivante pour afficher les opérations de géo-réplication plus récentes associées à la base de données MyDB.

        SELECT * FROM sys.dm_operation_status where major_resource_is = 'MyDB'
        ORDER BY start_time DESC

9. Cliquez sur **Exécuter** pour exécuter la requête.


## Étapes suivantes

- [Exercices de récupération d’urgence](sql-database-disaster-recovery-drills.md)


## Ressources supplémentaires

- [Coup de projecteur sur les nouvelles fonctionnalités de géo-réplication](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Conception d’applications cloud pour la continuité d’activité à l’aide de la géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](sql-database.md)

<!---HONumber=AcomDC_0128_2016-->