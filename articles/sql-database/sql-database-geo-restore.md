<properties
   pageTitle="Continuité d’activité dans le Cloud - Géo-restauration | Microsoft Azure"
   description="Découvrez comment Azure SQL Database prend en charge la continuité des activités cloud et la récupération de base de données et vous aide à maintenir les applications cloud opérationnelles."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
   ms.date="06/17/2016"
   ms.author="sstein"/>

# Vue d’ensemble : Restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante

> [AZURE.SELECTOR]
- [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Restauration dans le temps](sql-database-point-in-time-restore.md)
- [Restauration d’une base de données supprimée](sql-database-restore-deleted-database.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Scénarios de continuité des activités](sql-database-business-continuity-scenarios.md)


La géo-restauration vous permet de restaurer une base de données SQL sur n’importe quel serveur dans n’importe quelle région Azure à partir de la dernière [sauvegarde quotidienne automatisée](sql-database-automated-backups.md). La géo-restauration utilise une sauvegarde géo-redondante en tant que source et peut être mise à profit pour récupérer une base de données même si la base de données ou le centre de données est inaccessible en raison d’une défaillance. Vous pouvez utiliser le [portail Azure](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md) ou [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-geo-restore.md)
- [Portail Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

La géo-restauration constitue l’option de récupération par défaut lorsque votre base de données est indisponible en raison d’un incident dans la région où la base de données est hébergée. La base de données peut être créée sur n’importe quel serveur dans n’importe quelle région Azure. La géo-restauration s’appuie sur des [sauvegardes de base de données automatisées](sql-database-automated-backups.md) dans le stockage Azure géo-redondant et restaure à partir de la copie de sauvegarde géo-répliquée. Elle résiste par conséquent aux pannes de stockage dans la région primaire.

## La géo-restauration en détail

La géo-restauration utilise la même technologie que la limite de restauration dans le temps, avec toutefois une différence importante. Elle restaure la base de données à partir d’une copie de la sauvegarde quotidienne la plus récente dans le stockage d’objets blob géo-répliqué (RA-GRS). Pour chaque base de données active, le service gère une chaîne de sauvegarde qui inclut une sauvegarde hebdomadaire complète, plusieurs sauvegardes différentielles quotidiennes et des journaux de transactions enregistrés toutes les 5 minutes. Ces objets blob sont répliqués, ce qui garantit la disponibilité des sauvegardes quotidiennes même après une défaillance importante dans la région primaire. L’image ci-dessous illustre la géoréplication des sauvegardes hebdomadaires et quotidiennes copiées vers les conteneurs de stockage.

![géo-restauration](./media/sql-database-geo-restore/geo-restore-1.png)


Si un incident à grande échelle dans une région entraîne l’indisponibilité de votre application de base de données, vous pouvez utiliser la géo-restauration pour restaurer une base de données à partir de la sauvegarde la plus récente sur un serveur situé dans n’importe quelle autre région. Toutes les sauvegardes sont géo-répliquées et peuvent présenter un délai entre le moment où la sauvegarde est effectuée et celui où elle est géo-répliquée dans l’objet blob Azure dans une autre région. Ce délai peut atteindre une heure. En cas d’incident, il peut donc y avoir jusqu’à une heure de pertes de données, c’est-à-dire que le RPO peut être égal à une heure. L’image ci-dessous illustre la restauration de la base de données à partir de la dernière sauvegarde quotidienne.


![géo-restauration](./media/sql-database-geo-restore/geo-restore-2.png)

Utilisez [Obtenir une base de données récupérable](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) pour obtenir le dernier point de restauration avec réplication géographique.

## Temps de récupération pour une géo-restauration

Le temps de récupération dépend de plusieurs facteurs : la taille de la base de données et le niveau de performance de celle-ci, ainsi que le nombre de demandes de restauration simultanées en cours de traitement dans la région cible. En cas de panne prolongée dans une région, il est possible qu’un grand nombre de demandes de géo-restauration soient traitées par les autres régions. S’il y a un grand nombre de demandes, le temps de récupération des bases de données de cette région peut s’en trouver augmenté. La durée nécessaire pour restaurer une base de données dépend de plusieurs facteurs, tels que la taille de la base de données, le nombre de journaux de transactions, la bande passante réseau, etc. La majorité des restaurations de bases de données se terminent dans un délai de 12 heures.

## Résumé

Bien que la géo-restauration soit disponible pour tous les niveaux de service, il s’agit de la solution de récupération d’urgence la plus basique proposée dans la base de données SQL Azure, avec le RPO et le temps de récupération estimé (ERT) les plus longs. Pour les bases de données de base dont la taille ne dépasse pas 2 Go, la géo-restauration offre une solution de récupération d’urgence intéressante avec un ERT de 12 heures. Pour les bases de données Standard ou Premium plus volumineuses, vous pouvez utiliser la géoréplication active pour bénéficier de temps de récupération plus courts ou réduire le risque de perte de données. La géoréplication active offre un RPO et un ERT nettement inférieurs, car elle nécessite simplement l’initialisation d’un basculement vers une base de données secondaire répliquée en continu. Pour plus d’informations, consultez l’article [Géo-réplication active](sql-database-geo-replication-overview.md).

## Étapes suivantes

- Pour obtenir des instructions détaillées sur la restauration d’une base de données SQL Azure à l’aide du portail Azure à partir d’une sauvegarde géo-redondante, consultez [Géo-restauration à l’aide du portail Azure](sql-database-geo-restore-portal.md)
- Pour obtenir des instructions détaillées sur la restauration d’une base de données SQL Azure à l’aide de PowerShell à partir d’une sauvegarde géo-redondante, consultez [Géo-restauration à l’aide de PowerShell](sql-database-geo-restore-powershell.md)
- Pour une discussion complète sur la récupération après une panne, consultez [Récupération après une panne](sql-database-disaster-recovery.md)

## Ressources supplémentaires

- [Scénarios de continuité des activités](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->