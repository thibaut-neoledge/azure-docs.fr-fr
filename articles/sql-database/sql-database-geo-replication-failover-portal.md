<properties 
    pageTitle="Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec le portail Azure | Microsoft Azure" 
    description="Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec le portail Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="07/19/2016"
    ms.author="sstein"/>

# Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec le portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Cet article montre comment lancer le basculement vers une base de données SQL secondaire avec le [portail Azure](http://portal.azure.com). Pour configurer la géoréplication, consultez [Configurer la géoréplication pour Base de données SQL Azure](sql-database-geo-replication-portal.md).


## Initialisation d’un basculement

La base de données secondaire peut être basculée en base de données primaire.

1. Dans le [portail Azure](http://portal.azure.com), accédez à la base de données primaire dans le partenariat de géoréplication.
2. Dans le panneau de la base de données SQL, sélectionnez **Tous les paramètres** > **Géo-réplication**.
3. Dans la liste des bases de données **SECONDAIRES**, sélectionnez la base de données qui doit devenir la nouvelle base de données primaire et cliquez sur **Basculement**.

    ![basculement][2]

4. Cliquez sur **Oui** pour commencer le basculement.

La commande fait immédiatement basculer la base de données secondaire vers le rôle primaire.

Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (de l’ordre de 0 à 25 secondes) pendant que les rôles sont activés. Si la base de données primaire comporte plusieurs bases de données secondaires, la commande reconfigure automatiquement les autres bases de données secondaires pour qu’elles se connectent à la nouvelle base de données primaire. Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales.

>[AZURE.NOTE] Si la base de données primaire est en ligne et valide des transactions lorsque la commande est émise, une perte de données peut se produire.


## Étapes suivantes   

- Pour en savoir plus sur la reprise après un sinistre à l’aide de la géo-réplication active, y compris les étapes de pré/post-récupération et la simulation d’une récupération d’urgence, consultez [Exercices de récupération d’urgence](sql-database-disaster-recovery.md)
- Pour lire un billet de blog publié par Sasha Nosov concernant la géo-réplication active, consultez [Coup de projecteur sur les nouvelles fonctionnalités de géo-réplication](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Pour plus d’informations sur la conception d’applications cloud afin d’utiliser la géo-réplication active, consultez [Conception d’applications cloud pour la continuité d’activité à l’aide de la géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Pour plus d’informations sur l’utilisation de la géo-réplication active avec des pools de bases de données élastiques, consultez [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Pour une vue d’ensemble de la continuité des activités, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)




<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png

<!---HONumber=AcomDC_0727_2016-->