<properties 
   pageTitle="Récupération d’erreur utilisateur de la base de données SQL | Microsoft Azure" 
   description="Découvrez comment récupérer le système après une erreur d’un utilisateur, une altération accidentelle des données ou une base de données supprimée à l’aide de la fonctionnalité de limite de restauration dans le temps de la base de données SQL Microsoft Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Récupération d’une base de données SQL Microsoft Azure suite à une erreur de l’utilisateur

La base de données SQL Microsoft Azure offre deux fonctionnalités essentielles pour effectuer une récupération suite à une erreur de l’utilisateur ou à la modification involontaire de données.

- [Restauration dans le temps](sql-database-point-in-time-restore.md) 
- [Restauration d’une base de données supprimée](sql-database-restore-deleted-database.md)

La base de données SQL Azure effectue toujours les restaurations vers une nouvelle base de données. Ces fonctions de restauration sont proposées pour toutes les bases de données, quel que soit leur niveau (niveau de base, Standard et Premium).

##Restauration dans le temps

En cas d’erreur de l’utilisateur ou de modification involontaire des données, vous pouvez utiliser la fonction de limite de restauration dans le temps pour restaurer une base de données à un moment donné, inclus dans la période de rétention prévue pour vos bases de données.

Les bases de données de niveau de base proposent une période de rétention de 7 jours. Quant aux niveaux Standard et Premium, ils offrent des périodes de rétention de 14 et 35 jours, respectivement. Pour en savoir plus sur la rétention de sauvegarde de base de données, consultez [Sauvegardes automatisées](sql-database-automated-backups.md).

Pour effectuer une limite de restauration dans le temps, consultez les ressources suivantes :

- [Limite de restauration dans le temps avec le portail Azure](sql-database-point-in-time-restore-portal.md)
- [Limite de restauration dans le temps avec PowerShell](sql-database-point-in-time-restore-powershell.md)
- [Limite de restauration dans le temps avec l’API REST (createmode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 


## restauration d’une base de données supprimée.

En cas de suppression d’une base de données, la base de données SQL Microsoft Azure permet de la restaurer au moment de cette suppression. La base de données SQL Microsoft Azure stocke la sauvegarde de base de données supprimée pendant la période de rétention de la base de données.

La période de rétention d’une base de données supprimée est déterminée par le niveau de service de la base de données pendant sa période d’existence, ou le nombre de jours d’existence de la base de données. La valeur la plus faible est choisie. Pour en savoir plus sur la rétention de base de données, consultez [Sauvegardes automatisées](sql-database-automated-backups.md).

Pour restaurer une base de données supprimée :

- [Restaurer une base de données avec le portail Azure](sql-database-restore-deleted-database-portal.md)
- [Restaurer une base de données avec PowerShell](sql-database-restore-deleted-database-powershell.md)
- [Restaurer une base de données supprimée avec l’API REST (createmode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


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