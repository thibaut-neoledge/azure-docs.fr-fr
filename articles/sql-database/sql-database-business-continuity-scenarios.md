<properties
	pageTitle="Scénarios de continuité des activités de la base de données SQL Azure | Microsoft Azure"
	description="Scénarios de continuité des activités de la base de données SQL Azure"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/16/2016"
	ms.author="carlrab"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Scénarios de continuité des activités pour la base de données SQL Azure

> [AZURE.SELECTOR]
- [Continuité des activités](sql-database-business-continuity.md)
- [Scénarios](sql-database-business-continuity-scenarios.md)
- [Restauration dans le temps](sql-database-point-in-time-retore.md)
- [Restauration d’une base de données supprimée](sql-database-restore-deleted-database.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication)

Cet article vous présente plusieurs scénarios de continuité des activités de la base de données SQL Azure.

## Récupération après une panne

La section [Restaurer une base de données SQL Azure ou basculer vers une base de données secondaire](sql-database-disaster-recovery.md) décrit comment récupérer après une panne en utilisant les fonctionnalités suivantes :

- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Restauration géographique](sql-database-geo-restore.md)

Cet article indique quand effectuer la récupération, comment récupérer à l’aide de chaque fonctionnalité, comment configurer votre base de données après récupération et comment configurer votre application après la récupération.

## Récupération suite à une erreur de l'utilisateur

La section [Récupération d’une base de données SQL Microsoft Azure suite à une erreur de l’utilisateur](sql-database-user-error-recovery.md) décrit comment récupérer les erreurs de l’utilisateur ou en cas de modification involontaire des données à l’aide des fonctionnalités suivantes :

- [Restauration dans le temps](sql-database-point-in-time-restore.md) 
- [Restauration d’une base de données supprimée](sql-database-restore-deleted-database.md)

## Simulation d’une récupération d'urgence

La section [Simulation d’une récupération d'urgence](sql-database-disaster-recovery-drills.md) décrit comment effectuer une simulation de récupération d’urgence en utilisant les fonctionnalités suivantes :

- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Restauration géographique](sql-database-geo-restore.md)

Nous recommandons de valider régulièrement la préparation des applications à la récupération. La vérification du comportement de l'application et des implications en matière de pertes de données et/ou d'interruptions en cas basculement constitue une bonne pratique. Il s'agit également d'une exigence figurant dans la plupart des normes industrielles dans le cadre d'une certification de la continuité des activités.

L'exécution d'un exercice de récupération d'urgence comprend :

- la simulation d'une défaillance des couches de données
- la récupération 
- la validation de l'intégrité des applications après la récupération

## Gestion de la sécurité après la récupération d’urgence

La section [Gestion de la sécurité après la récupération d’urgence](sql-database-geo-replication-security-config.md) décrit les exigences d’authentification requises pour configurer et contrôler la [géo-réplication active](sql-database-geo-replication-overview.md) et les opérations requises pour configurer l’accès utilisateur à la base de données secondaire. Elle explique également comment activer l’accès à la base de données restaurée après l’utilisation de la [géo-restauration](sql-database-geo-restore.md).

## Gestion des mises à niveau propagées des applications cloud à l’aide de la géo-réplication active

La section [Gestion des mises à niveau propagées des applications cloud à l’aide de la géo-réplication active de la base de données SQL](sql-database-manage-application-rolling-upgrade.md) décrit comment utiliser la [géo-réplication](sql-database-geo-replication-overview.md) dans la base de données SQL pour activer les mises à niveau de votre application cloud. Une mise à niveau est une opération qui entraîne une interruption de service ; il est donc recommandé de l’intégrer à votre conception et à votre planification de la continuité des activités. Cet article présente deux méthodes différentes permettant d’orchestrer le processus de mise à niveau propagée, et précise les avantages et inconvénients de chaque option.

## Concevoir une application pour la récupération d’urgence cloud à l’aide de la géo-réplication active

La section [Concevoir une application pour la récupération d’urgence cloud à l’aide de la géo-réplication active dans une base de données SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) décrit comment utiliser la [géo-réplication active](sql-database-geo-replication-overview.md) dans la base de données SQL pour concevoir des applications de base de données résistant aux défaillances régionales et aux pannes graves. Cet article prend en compte la topologie de déploiement d’applications, le contrat de niveau de service que vous ciblez, la latence du trafic et les coûts, puis examine les modèles d’application courants et présenter les avantages et les inconvénients de chaque option.

## Stratégies de récupération d’urgence pour les applications utilisant des pools de bases de données élastiques

La section [Stratégies de récupération d’urgence pour les applications utilisant le pool élastique de base de données SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) décrit les scénarios de récupération d’urgence à l’aide de [pools de bases de données élastiques](sql-database-elastic-pool.md).

## Étapes suivantes

- Pour plus d’informations sur l’utilisation et la configuration de la géo-réplication active pour la récupération d’urgence, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)
- Pour plus d’informations sur l’utilisation de la restauration géographique pour la récupération d’urgence, consultez [Restauration géographique](sql-database-geo-restore.md)

## Ressources supplémentaires

- [Continuité des activités et récupération d’urgence d’une base de données SQL Azure](sql-database-business-continuity.md)
- [Limite de restauration dans le temps](sql-database-point-in-time-restore.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)
- [Configuration de la sécurité de la géo-réplication](sql-database-geo-replication-security-config.md)
- [FAQ sur la continuité d’activité et la récupération d’urgence des bases de données SQL](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->