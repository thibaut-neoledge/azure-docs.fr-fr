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
   ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Scénarios de continuité des activités pour la base de données SQL Azure

Cet article présente plusieurs scénarios de récupération d’urgence et plusieurs scénarios de conception d’application pour la continuité d’activité.

## Récupération après une panne

En cas de panne, la section [Récupérer une base de données SQL Azure en cas de défaillance](sql-database-disaster-recovery.md) explique comment utiliser une des suivantes solutions de continuité d’activité pour effectuer une récupération après une panne :

- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Restauration géographique](sql-database-recovery-using.backups.md#geo-restore)

Les étapes spécifiques et le délai nécessaire pour récupérer après une panne varient en fonction de la solution de continuité d’activité d’entreprise que vous choisissez. Toutefois, quelle que soit votre solution de continuité d’activité, vous devez savoir quand lancer la récupération, quelles sont les étapes de récupération de la base de données pour chaque solution de continuité, comment configurer votre base de données après récupération et comment configurer votre application après la récupération pour terminer votre récupération après une panne.

## Récupération après une erreur

En cas d’erreur d’un utilisateur ou de toute autre erreur consécutive à une modification involontaire des données, la section [Récupération d’une base de données SQL Azure suite à une erreur](sql-database-user-error-recovery.md) explique comment utiliser une des suivantes solutions de continuité d’activité pour effectuer une récupération après une erreur :

- [Limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Restauration d’une base de données supprimée](sql-database-recovery-using-backups.md#deleted-database-restore)

Les étapes spécifiques et le délai nécessaire pour récupérer après une panne varient en fonction de la solution de continuité d’activité d’entreprise que vous choisissez. Toutefois, quelle que soit votre solution de continuité d’activité, vous devez savoir comment récupérer après une erreur pour chaque solution de continuité.

## Simulation d’une récupération d'urgence en cas de panne

Pour que votre solution de continuité d’activité soit efficace, nous recommandons de valider régulièrement la préparation des applications à la récupération. La vérification du comportement de l'application et des implications en matière de pertes de données et/ou d'interruptions en cas basculement constitue une bonne pratique. Il s'agit également d'une exigence figurant dans la plupart des normes industrielles dans le cadre d'une certification de la continuité des activités.

L'exécution d'un exercice de récupération d'urgence comprend :

- la simulation d'une défaillance des couches de données
- la récupération
- la validation de l'intégrité des applications après la récupération

La section [Simulation d’une récupération d'urgence](sql-database-disaster-recovery-drills.md) décrit comment effectuer une simulation de récupération d’urgence en utilisant une des solutions de continuité d’activité suivantes :

- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Restauration géographique](sql-database-recovery-using-backups.md#geo-restore)

## Gestion des mises à niveau propagées des applications cloud à l’aide de la géo-réplication active

La mise à niveau d’une application cloud avec une base de données SQL est une opération qui entraîne une interruption de service et, par conséquent, vous devez inclure ce scénario dans le cadre de la conception et de la planification de la continuité des activités. La section [Gestion des mises à niveau des applications](sql-database-manage-application-rolling-upgrade.md) explique comment utiliser la [géoréplication](sql-database-geo-replication-overview.md) dans la base de données SQL pour activer les mises à niveau propagées de votre application cloud. Cet article présente deux méthodes différentes permettant d’orchestrer le processus de mise à niveau propagée, et précise les avantages et inconvénients de chaque option.

## Concevoir une application pour la récupération d’urgence cloud à l’aide de la géo-réplication active

La section [Concevoir une application pour la récupération d’urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) décrit comment utiliser la [géo-réplication active](sql-database-geo-replication-overview.md) dans la base de données SQL pour concevoir des applications de base de données résistant aux défaillances régionales et aux pannes graves. Cet article prend en compte la topologie de déploiement d’applications, le contrat de niveau de service que vous ciblez, la latence du trafic et les coûts, puis examine les modèles d’application courants et présenter les avantages et les inconvénients de chaque option.

## Stratégies de récupération d’urgence pour les applications utilisant des pools de bases de données élastiques

La section [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) décrit les scénarios de récupération d’urgence à l’aide de [pools de bases de données élastiques](sql-database-elastic-pool.md).

## Étapes suivantes

- Pour une vue d’ensemble de la continuité des activités, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
- Pour en savoir plus sur la conception de la continuité des activités et les scénarios de récupération, consultez [Scénarios de continuité des activités](sql-database-business-continuity-scenarios.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)

<!---HONumber=AcomDC_0629_2016-->