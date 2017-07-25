---
title: "TSQL : Lancer un basculement pour Azure SQL Database | Microsoft Docs"
description: "Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec Transact-SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5eb2d256-025d-4f5a-99d4-17f702b37f14
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 01/10/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 459941d2c82e5d4ef62beab4ccf775ab8f5efce4
ms.contentlocale: fr-fr
ms.lasthandoff: 06/22/2017


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-transact-sql"></a>Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec Transact-SQL

Cet article montre comment lancer le basculement vers une base de données SQL secondaire avec Transact-SQL. Pour configurer la géoréplication, voir [Configurer la géoréplication pour Azure SQL Database](sql-database-geo-replication-transact-sql.md).

Pour lancer le basculement, vous devez disposer de ce qui suit :

* ID de connexion DBManager sur le serveur principal
* Droits db_ownership sur la base de données locale que vous allez géorépliquer
* Rôle DBManager sur les serveurs partenaires sur lesquels vous voulez configurer la géoréplication
* Dernière version de SQL Server Management Studio (SSMS)

> [!IMPORTANT]
> Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>  

## <a name="initiate-a-planned-failover-promoting-a-secondary-database-to-become-the-new-primary"></a>Initier un basculement planifié en assurant la promotion d’une base de données secondaire, pour qu’il devienne le nouveau réplica principal
Vous pouvez utiliser l’instruction **ALTER DATABASE** pour promouvoir une base de données secondaire afin qu’elle devienne la nouvelle base de données primaire de manière planifiée, et rétrogradant l’élément primaire existant pour qu’elle devienne secondaire. Cette instruction est exécutée sur la base de données master sur le serveur logique de base de données SQL Azure sur lequel réside la base secondaire géo-répliquée promue. Cette fonctionnalité est conçue pour le basculement planifié, comme pendant les exercices de récupération d’urgence et nécessite que la base de données primaire soit disponible.

La commande exécute le flux de travail suivant :

1. Bascule provisoirement la réplication en mode synchrone, ce qui fait que toutes les transactions en attente doivent être vidées vers le serveur secondaire et toutes les nouvelles transactions bloquées ;
2. Inverse les rôles des deux bases de données du partenariat de géoréplication.  

Cette séquence garantit que les deux bases de données sont synchronisées avant le basculement des rôles et que, par conséquent, aucune perte de données ne se produira. Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (de l’ordre de 0 à 25 secondes) pendant que les rôles sont activés. Si la base de données primaire comporte plusieurs bases de données secondaires, la commande reconfigure automatiquement les autres bases de données secondaires pour qu’elles se connectent à la nouvelle base de données primaire.  Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales. Pour plus d’informations, consultez [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et [Niveaux de service](sql-database-service-tiers.md).

Utilisez les étapes suivantes pour initier un basculement planifié.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure dans lesquels réside une base de données secondaire répliquée.
2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction **ALTER DATABASE** suivante pour basculer la base de données secondaire vers le rôle primaire.
   
        ALTER DATABASE <MyDB> FAILOVER;
4. Cliquez sur **Exécuter** pour exécuter la requête.

> [!NOTE]
> Dans de rares cas, il est possible que l’opération ne puisse pas s’achever et semble bloquée. Dans ce cas, l’utilisateur peut exécuter la commande de basculement forcé et accepter la perte de données.
> 
> 

## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales
Vous pouvez utiliser l’instruction **ALTER DATABASE** pour promouvoir une base de données secondaire afin qu’elle devienne la nouvelle base de données primaire de façon non planifiée, en forçant la rétrogradation de la base de données primaire existante pour qu’elle devienne secondaire dans l’hypothèse où elle cesserait d’être disponible. Cette instruction est exécutée sur la base de données master sur le serveur logique de base de données SQL Azure sur lequel réside la base secondaire géo-répliquée promue.

Cette fonctionnalité est conçue pour la récupération d’urgence lorsque la restauration de la disponibilité de la base de données est essentielle et une perte de données est acceptable. Lorsque le basculement forcé est appelé, la base de données secondaire devient la base de données primaire immédiatement et commence à accepter des transactions d’écriture. Dès que la base de données primaire d’origine est en mesure de se reconnecter à cette base de données primaire, une sauvegarde incrémentielle s’effectue sur la base de données primaire d’origine et l’ancienne base de données primaire est transformée en base de données secondaire de la nouvelle base de données primaire ; par la suite, il s’agit d’un simple réplica de la nouvelle base de données primaire.

Toutefois, étant donné que la limite de restauration n’est pas prise en charge sur les bases de données secondaires, si l’utilisateur souhaite récupérer les données validées dans l’ancienne base de données primaire qui n’ont pas été répliquées dans la nouvelle base de données primaire avant le basculement forcé, l’utilisateur devra faire appel à l’assistance technique pour récupérer les données perdues.

Si la base de données primaire comporte plusieurs bases de données secondaires, la commande reconfigure automatiquement les autres bases de données secondaires pour qu’elles se connectent à la nouvelle base de données primaire.

Utilisez les étapes suivantes pour lancer un basculement non planifié.

1. Dans Management Studio, connectez-vous à un serveur logique de base de données SQL Azure dans lesquels réside une base de données secondaire répliquée.
2. Ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction **ALTER DATABASE** suivante pour basculer la base de données secondaire vers le rôle primaire.
   
        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;
4. Cliquez sur **Exécuter** pour exécuter la requête.

> [!NOTE]
> Si la commande est émise lorsque les bases de données primaire et secondaire sont en ligne, l’ancienne base de données primaire devient immédiatement la nouvelle base de données secondaire, sans synchronisation des données. Si la base de données primaire valide des transactions lorsque la commande est émise, une perte de données peut se produire.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Après le basculement, assurez-vous que les exigences d’authentification de votre serveur et de votre base de données sont configurées sur la nouvelle base de données primaire. Pour plus d’informations, consultez [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md).
* Pour en savoir plus sur la récupération après sinistre à l’aide de la géoréplication active, notamment les étapes de pré/post-récupération et l’organisation d’un exercice de récupération d’urgence, voir [Récupération d’urgence](sql-database-disaster-recovery.md).
* Consultez le billet de blog publié par Sasha Nosov concernant la géoréplication active : [Coup de projecteur sur les nouvelles fonctionnalités de géoréplication](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/).
* Pour plus d’informations sur la conception d’applications cloud afin d’utiliser la géoréplication active, voir [Conception d’applications cloud pour la continuité d’activité à l’aide de la géoréplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md).
* Pour plus d’informations sur la géoréplication active avec des pools élastiques, voir [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
* Pour une vue d’ensemble de la continuité des activités, voir [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).


