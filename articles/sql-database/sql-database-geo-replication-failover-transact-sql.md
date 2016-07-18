<properties 
    pageTitle="Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec Transact-SQL | Microsoft Azure" 
    description="Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec Transact-SQL" 
    services="sql-database" 
    documentationCenter="" 
    authors="carlrabeler" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="04/27/2016"
    ms.author="carlrab"/>

# Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec Transact-SQL


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Cet article montre comment lancer le basculement vers une base de données SQL secondaire avec Transact-SQL. Pour configurer la géoréplication, consultez [Configurer la géoréplication pour Base de données SQL Azure](sql-database-geo-replication-transact-sql.md).



Pour lancer le basculement, vous avez besoin des éléments suivants :

- Une connexion qui est DBManager sur le serveur principal, a la propriété db\_ownership de la base de données locale que vous allez géo-répliquer et est DBManager sur le ou les serveurs partenaires sur lesquels vous allez configurer la géoréplication.
- SQL Server Management Studio (SSMS)


> [AZURE.IMPORTANT] Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).




## Initier un basculement planifié en assurant la promotion d’une base de données secondaire, pour qu’il devienne le nouveau réplica principal

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour promouvoir une base de données secondaire afin qu’elle devienne la nouvelle base de données primaire de manière planifiée, et rétrogradant l’élément primaire existant pour qu’elle devienne secondaire. Cette instruction est exécutée sur la base de données master sur le serveur logique de base de données SQL Azure sur lequel réside la base secondaire géo-répliquée promue. Cette fonctionnalité est conçue pour le basculement planifié, comme pendant les exercices de récupération d’urgence et nécessite que la base de données primaire soit disponible.

La commande exécute le flux de travail suivant :

1. Bascule provisoirement la réplication en mode synchrone, ce qui fait que toutes les transactions en attente doivent être vidées vers le serveur secondaire et toutes les nouvelles transactions bloquées ;

2. Inverse les rôles des deux bases de données du partenariat géoréplication.

Cette séquence garantit que les deux bases de données sont synchronisées avant le basculement des rôles et que, par conséquent, aucune perte de données ne se produira. Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (de l’ordre de 0 à 25 secondes) pendant que les rôles sont activés. Si la base de données primaire comporte plusieurs bases de données secondaires, la commande reconfigure automatiquement les autres bases de données secondaires pour qu’elles se connectent à la nouvelle base de données primaire. Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales. Pour plus d’informations, voir [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et [Niveaux de service](sql-database-service-tiers.md).


Utilisez les étapes suivantes pour initier un basculement planifié.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure dans lesquels réside une base de données secondaire répliquée.

2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour basculer la base de données secondaire vers le rôle primaire.

        ALTER DATABASE <MyDB> FAILOVER;

4. Cliquez sur **Exécuter** pour exécuter la requête.

>[AZURE.NOTE] Dans de rares cas, il est possible que l’opération ne puisse pas s’achever et semble bloquée. Dans ce cas, l’utilisateur peut exécuter la commande de basculement forcé et accepter la perte de données.


## Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour promouvoir une base de données secondaire afin qu’elle devienne la nouvelle base de données primaire de façon non planifiée, en forçant la rétrogradation de l’élément primaire existant pour qu’elle devienne secondaire dans le cas où la base de données n’est plus disponible. Cette instruction est exécutée sur la base de données master sur le serveur logique de base de données SQL Azure sur lequel réside la base secondaire géo-répliquée promue.

Cette fonctionnalité est conçue pour la récupération d’urgence lorsque la restauration de la disponibilité de la base de données est essentielle et une perte de données est acceptable. Lorsque le basculement forcé est appelé, la base de données secondaire devient la base de données primaire immédiatement et commence à accepter des transactions d’écriture. Dès que la base de données primaire d’origine est en mesure de se reconnecter à cette base de données primaire, une sauvegarde incrémentielle s’effectue sur la base de données primaire d’origine et l’ancienne base de données primaire est transformée en base de données secondaire de la nouvelle base de données primaire ; par la suite, il s’agit d’un simple réplica de la nouvelle base de données primaire.

Toutefois, étant donné que la limite de restauration n’est pas prise en charge sur les bases de données secondaires, si l’utilisateur souhaite récupérer les données validées dans l’ancienne base de données primaire qui n’ont pas été répliquées dans la nouvelle base de données primaire avant le basculement forcé, l’utilisateur devra faire appel à l’assistance technique pour récupérer les données perdues.

Si la base de données primaire comporte plusieurs bases de données secondaires, la commande reconfigure automatiquement les autres bases de données secondaires pour qu’elles se connectent à la nouvelle base de données primaire.

Utilisez les étapes suivantes pour lancer un basculement non planifié.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure dans lesquels réside une base de données secondaire répliquée.

2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour basculer la base de données secondaire vers le rôle primaire.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Cliquez sur **Exécuter** pour exécuter la requête.

>[AZURE.NOTE] Si la commande est émise lorsque les bases de données primaire et secondaire sont en ligne, l’ancienne base de données primaire deviendra immédiatement la nouvelle base de données secondaire, sans synchronisation des données. Si la base de données primaire valide des transactions lorsque la commande est émise, une perte de données peut se produire.



## Ressources supplémentaires

- [Coup de projecteur sur les nouvelles fonctionnalités de géoréplication](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Conception d’applications cloud pour la continuité d’activité à l’aide de la géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/services/sql-database/)
- [Exercices de récupération d'urgence](sql-database-disaster-recovery-drills.md)

<!---HONumber=AcomDC_0706_2016-->