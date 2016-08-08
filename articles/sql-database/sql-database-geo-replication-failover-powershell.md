<properties 
    pageTitle="Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec PowerShell | Microsoft Azure" 
    description="Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="07/19/2016"
    ms.author="sstein"/>

# Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec PowerShell



> [AZURE.SELECTOR]
- [Portail Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Cet article vous montre comment lancer un basculement planifié ou non planifié pour une base de données SQL avec PowerShell. Pour configurer la géoréplication, consultez [Configurer la géoréplication pour Base de données SQL Azure](sql-database-geo-replication-powershell.md).



## Initier un basculement planifié

Utilisez l’applet de commande **Set-AzureRmSqlDatabaseSecondary** avec le paramètre **-Failover** pour promouvoir une base de données secondaire pour qu’elle devienne la nouvelle base de données primaire, rétrogradant ainsi la base de données primaire existante en base de données secondaire. Cette fonctionnalité est conçue pour un basculement planifié, comme au cours des exercices de récupération d’urgence et exige que la base de données primaire soit disponible.

La commande exécute le flux de travail suivant :

1. Basculer temporairement la réplication en mode synchrone. Les transactions en attente seront alors vidées sur la base de données secondaire.

2. Changer les rôles des deux bases de données dans le partenariat de géoréplication.

Cette séquence garantit que les deux bases de données sont synchronisées avant le basculement des rôles et que, par conséquent, aucune perte de données ne se produira. Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (de l’ordre de 0 à 25 secondes) pendant que les rôles sont activés. Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales. Pour plus d’informations, consultez [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).




Cette applet de commande sera renvoyée lorsque le processus de basculement de la base de données secondaire vers la primaire sera terminé.

La commande suivante bascule les rôles de la base de données nommée « mydb » sur le serveur « srv2 » sous le groupe de ressources « rg2 » vers la base de données primaire. La base de données primaire d’origine à laquelle « db2 » a été connectée bascule sur la base de données secondaire une fois que les deux bases de données sont entièrement synchronisées.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] Dans de rares cas, il est possible que l’opération ne puisse pas se terminer et ne réponde plus. Dans ce cas, l’utilisateur peut appeler la commande de basculement forcé (basculement non planifié) et accepter une perte de données.


## Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales


Vous pouvez utiliser l’applet de commande **Set-AzureRmSqlDatabaseSecondary** avec les paramètres **-Failover** et **-AllowDataLoss** pour promouvoir une base de données secondaire afin qu’elle devienne la nouvelle base de données primaire de manière non planifiée, forçant ainsi la rétrogradation de la base de données primaire existante en base de données secondaire quand la base de données primaire n’est plus disponible.

Cette fonctionnalité est conçue pour la récupération d’urgence lorsque la restauration de la disponibilité de la base de données est essentielle et une perte de données est acceptable. Lorsque le basculement forcé est appelé, la base de données secondaire devient la base de données primaire immédiatement et commence à accepter des transactions d’écriture. Dès que la base de données primaire d’origine est en mesure de se reconnecter à la nouvelle base de données primaire après l’opération de basculement forcé, une sauvegarde incrémentielle est effectuée sur la base de données primaire d’origine et l’ancienne base de données primaire est transformée en base de données secondaire de la nouvelle base de données primaire. Par conséquent, il s’agit simplement d’un réplica de la nouvelle base de données primaire.

Mais comme la limite de restauration dans le temps n’est pas prise en charge sur les bases de données secondaires, si vous souhaitez récupérer des données validées dans l’ancienne base de données primaire qui n’avait pas été répliquée sur la nouvelle base de données primaire, vous devez engager CSS pour restaurer une base de données dans la sauvegarde de journal connue.

> [AZURE.NOTE] Si la commande est émise lorsque les bases de données primaire et secondaire sont en ligne, l’ancienne base de données primaire deviendra immédiatement la nouvelle base de données secondaire, sans synchronisation des données. Si la base de données primaire valide des transactions lorsque la commande est émise, une perte de données peut se produire.


Si la base de données primaire compte plusieurs bases de données secondaires, la commande réussit partiellement. La base de données secondaire sur laquelle la commande a été exécutée deviendra la base de données primaire. L’ancienne base de données primaire reste toutefois primaire, en d’autres termes, les deux bases de données primaires sont finalement incompatibles et connectées par un lien de réplication suspendu. L’utilisateur doit alors réparer manuellement cette configuration à l’aide d’une API « suppression de base de données secondaire » sur une de ces bases de données primaires.


La commande suivante bascule les rôles de la base de données nommée « mydb » vers la base de données primaire si le serveur principal n’est pas disponible. La base de données d’origine à laquelle « mydb » était connectée va basculer sur la base de données secondaire une fois qu’elle est revenue est en ligne. À ce stade, la synchronisation peut entraîner une perte de données.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## Étapes suivantes   

- Pour en savoir plus sur la reprise après un sinistre à l’aide de la géo-réplication active, y compris les étapes de pré/post-récupération et la simulation d’une récupération d’urgence, consultez [Exercices de récupération d’urgence](sql-database-disaster-recovery.md)
- Pour lire un billet de blog publié par Sasha Nosov concernant la géo-réplication active, consultez [Coup de projecteur sur les nouvelles fonctionnalités de géo-réplication](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Pour plus d’informations sur la conception d’applications cloud afin d’utiliser la géo-réplication active, consultez [Conception d’applications cloud pour la continuité d’activité à l’aide de la géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Pour plus d’informations sur l’utilisation de la géo-réplication active avec des pools de bases de données élastiques, consultez [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Pour une vue d’ensemble de la continuité des activités, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)

<!---HONumber=AcomDC_0727_2016-->