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
   ms.workload="data-management"
   ms.date="05/10/2016"
   ms.author="sstein"/>

# Vue d’ensemble : Fonctionnalité de géo-restauration du service Base de données SQL

La géo-restauration vous permet de restaurer une base de données SQL à partir de la sauvegarde quotidienne la plus récente. Elle est automatiquement activée pour tous les niveaux de service, sans coûts supplémentaires. La géo-restauration utilise une sauvegarde géo-redondante en tant que source et peut être mise à profit pour récupérer une base de données même si la base de données ou le centre de données est inaccessible en raison d’une défaillance.

L’initialisation de la géo-restauration permet de créer une nouvelle base de données SQL sur n’importe quel serveur, dans n’importe quelle région Azure.


|Tâche (portail) | PowerShell | REST |
|:--|:--|:--|
| [Récupérer une base de données SQL à partir d’une copie dans une autre région](sql-database-geo-restore-portal.md) | [PowerShell](sql-database-geo-restore-powershell.md) | [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |



La géo-restauration constitue l’option de récupération par défaut lorsque votre base de données est indisponible en raison d’un incident dans la région où la base de données est hébergée. À l’image de la [limite de restauration dans le temps](sql-database-point-in-time-restore.md), la géo-restauration s’appuie sur les sauvegardes de base de données dans le stockage Azure géo-redondant. Elle restaure à partir de la copie de sauvegarde répliquées au niveau géographique. Donc, elle est résistante aux pannes de stockage dans la région principale.



## La géo-restauration en détail

La géo-restauration utilise la même technologie que la limite de restauration dans le temps, avec toutefois une différence importante. Elle restaure la base de données à partir d’une copie de la sauvegarde quotidienne la plus récente dans le stockage d’objets blob géo-répliqué (RA-GRS). Pour chaque base de données active, le service gère une chaîne de sauvegarde qui inclut une sauvegarde hebdomadaire complète, plusieurs sauvegardes différentielles quotidiennes et des journaux de transactions enregistrés toutes les 5 minutes. Ces objets blob sont répliqués, ce qui garantit la disponibilité des sauvegardes quotidiennes même après une défaillance importante dans la région primaire. L’image ci-dessous illustre la géo-réplication des sauvegardes hebdomadaires et quotidiennes copiées vers les conteneurs de stockage.

![géo-restauration](./media/sql-database-geo-restore/geo-restore-1.png)


Si un incident à grande échelle dans région entraîne l’indisponibilité de votre application de base de données, vous pouvez utiliser la géo-restauration pour restaurer une base de données à partir de la sauvegarde la plus récente sur un serveur situé dans n’importe quelle autre région. Toutes les sauvegardes sont géo-répliquées et peuvent présenter un délai entre le moment où la sauvegarde est effectuée et celui où elle est géo-répliquée dans l’objet blob Azure dans une autre région. Ce délai peut atteindre une heure. En cas d’incident, il peut donc y avoir jusqu’à une heure de pertes de données, c’est-à-dire que le RPO peut être égal à une heure. L’image ci-dessous illustre la restauration de la base de données à partir de la dernière sauvegarde quotidienne.


![géo-restauration](./media/sql-database-geo-restore/geo-restore-2.png)



## Temps de récupération pour une géo-restauration

Le temps de récupération dépend de plusieurs facteurs : la taille de la base de données et le niveau de performance de celle-ci, ainsi que le nombre de demandes de restauration simultanées en cours de traitement dans la région cible. En cas de panne prolongée dans une région, il est possible qu’un grand nombre de demandes de géo-restauration soient traitées par les autres régions. S’il y a un grand nombre de demandes, le temps de récupération des bases de données de cette région peut s’en trouver augmenté.


## Résumé

Bien que la géo-restauration soit disponible pour tous les niveaux de service, il s’agit de la solution de récupération d’urgence la plus basique proposée dans la base de données SQL Azure, avec le RPO et le temps de récupération estimé (ERT) les plus longs. Pour les bases de données de base dont la taille ne dépasse pas 2 Go, la géo-restauration offre une solution de récupération d’urgence intéressante avec un ERT de 12 heures. Pour les bases de données Standard ou Premium plus volumineuses, vous pouvez utiliser la géo-réplication active pour bénéficier de temps de récupération plus courts ou réduire le risque de perte de données. La géo-réplication active offre un RPO et un ERT nettement inférieurs, car elle nécessite simplement l’initialisation d’un basculement vers une base de données secondaire répliquée en continu. Pour plus d’informations, consultez l’article [Géo-réplication active](sql-database-geo-replication-overview.md).

## Ressources supplémentaires

- [FAQ sur la continuité d’activité et la récupération d’urgence des bases de données SQL](sql-database-bcdr-faq.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Limite de restauration dans le temps](sql-database-point-in-time-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0511_2016-->