<properties
   pageTitle="Continuité d’activité dans le Cloud - Restauration d’une base de données supprimée - Base de données SQL | Microsoft Azure"
   description="Apprenez-en plus sur la limite de restauration dans le temps, qui vous permet de restaurer une base de données SQL Azure à un point antérieur dans le temps (jusqu’à 35 jours)."
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
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Récupérer une base de données SQL Azure à l’aide des sauvegardes automatisées d’une base de données

La base de données SQL fournit trois options pour la récupération de base de données à l’aide des [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md). Vous pouvez restaurer une base de données à partir des sauvegardes initiées par le service lors de leur [période de rétention](sql-database-service-tiers.md) vers :

- Une nouvelle base de données sur le même serveur logique récupéré à un point spécifié dans le temps durant la période de rétention.
- Une base de données sur le même serveur logique récupéré à l’heure de suppression pour une base de données supprimée.
- Une nouvelle base de données sur un serveur logique dans n’importe quelle région récupérée sur les sauvegardes quotidiennes plus récentes dans le stockage d’objets blob géo-répliqué (RA-GRS).

Vous pouvez également utiliser les [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md) pour créer une [copie de base de données](sql-database-copy.md) sur n’importe quel serveur logique dans n’importe quelle région qui est cohérente au niveau transactionnel avec la base de données SQL actuelle. Vous pouvez utiliser la copie de base de données et [l’exporter vers un fichier BACPAC](sql-database-export.md) pour archiver une copie cohérente au niveau transactionnel d’une base de données pour le stockage à long terme au-delà de la période de rétention, ou pour transférer une copie de votre base de données vers une instance de machine virtuelle Azure ou locale de SQL Server.

## Temps de récupération

Le temps de récupération pour restaurer une base de données à l’aide des sauvegardes automatisées d’une base de données est affecté par un certain nombre de facteurs :
 - la taille de la base de données ;
 - le niveau de performances de la base de données ;
 - le nombre de journaux de transactions impliqués ;
 - la quantité d’activité devant être relue pour effectuer une récupération au point de restauration ;
 - la bande passante du réseau, si la restauration s’effectue dans une autre région ;
 - le nombre de demandes de restauration simultanées en cours de traitement dans la région cible.
 
 Pour une base de données de très grande taille et/ou très active, la restauration peut prendre plusieurs heures. En cas de panne prolongée dans une région, il est possible qu’un grand nombre de demandes de géo-restauration soient traitées par les autres régions. S’il y a un grand nombre de demandes, le temps de récupération des bases de données de cette région peut s’en trouver augmenté. La majorité des restaurations de bases de données se terminent dans un délai de 12 heures.

 Il n'existe aucune fonctionnalité intégrée pour une restauration en bloc. Le script [Base de données SQL Azure : récupération de serveur complète](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) est un exemple d'une façon d'accomplir cette tâche.

## Limite de restauration dans le temps

La limite de restauration dans le temps vous permet de restaurer une base de données existante en tant que nouvelle base de données à un point antérieur dans le temps sur le même serveur logique à l’aide des [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md). Vous ne pouvez pas remplacer la base de données existante. Vous pouvez restaurer à un point antérieur dans le temps à l’aide du [portail Azure](sql-database-point-in-time-restore-portal.md), de [PowerShell](sql-database-point-in-time-restore-powershell.md) ou [de l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Limite de restauration dans le temps : portail Azure](sql-database-point-in-time-restore-portal.md)
- [Limite de restauration dans le temps : PowerShell](sql-database-point-in-time-restore-powershell.md)

La base de données peut être restaurée vers n’importe quel niveau de performance ou pool élastique. Vous devez vous assurer de disposer d’un quota DTU suffisant sur le serveur ou le pool élastique. N’oubliez pas que la restauration crée une nouvelle base de données, et que les performances et le niveau de service de la base de données restaurée peuvent être différents de ceux de la base de données d’origine. Une fois le processus terminé, la base de données restaurée est une base de données normale entièrement accessible en ligne, facturée aux tarifs habituels en fonction de ses performances et de son niveau de service. Aucun frais ne vous sera facturé jusqu’à ce que la restauration de la base de données soit terminée.

En règle générale, vous restaurez une base de données à un point antérieur à des fins de récupération. Dans ce cas, vous pouvez traiter la base de données restaurée comme remplacement de la base de données d’origine, ou l’utiliser pour en extraire des données afin de mettre à jour la base de données d’origine.

- ***Remplacement de la base de données :*** si la base de données restaurée est destinée à remplacer la base de données d’origine, vous devez vérifier que les performances et/ou le niveau de service sont appropriés, et effectuer une mise à l’échelle si nécessaire. Vous pouvez renommer la base de données d’origine, puis donner le nom d’origine à la base de données restaurée à l’aide de la commande ALTER DATABASE dans T-SQL.
- ***Récupération des données :*** si vous souhaitez récupérer des données à partir de la base de données restaurée suite à une erreur utilisateur ou d’application, vous devrez écrire et exécuter séparément les scripts de récupération de données pour extraire les données à partir de la base de données restaurée et les transférer vers la base de données d’origine. Bien que l’opération de restauration puisse prendre un certain temps, la base de données en cours de restauration sera visible dans la liste de bases de données pendant tout le processus. Si vous supprimez la base de données pendant la restauration, cela annule l’opération et vous ne serez pas facturé pour la base de données dont la restauration ne s’est pas terminée.

Pour plus d’informations sur l’utilisation de la limite de restauration dans le temps pour récupérer suite à des erreurs utilisateur et d’applications, consultez [Récupération suite à une erreur de l’utilisateur](sql-database-user-error-recovery.md)

## Restauration d’une base de données supprimée

La restauration d’une base de données supprimée vous permet de restaurer une base de données supprimée à l’heure de suppression sur le même serveur logique à l’aide des [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).

> [AZURE.IMPORTANT] Si vous supprimez une instance de serveur de base de données SQL Azure, toutes ses bases de données sont également supprimées et ne peuvent pas être récupérées. Il n'existe aucune prise en charge pour la restauration d'un serveur supprimé pour l'instant.

Vous pouvez utiliser le même nom ou un nouveau nom de base de données pour la base de données restaurée. Vous pouvez utiliser le [portail Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) ou [l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Restauration d’une base de données supprimée : portail Azure](sql-database-restore-deleted-database-portal.md)
- [Restauration d’une base de données supprimée : PowerShell](sql-database-restore-deleted-database-powershell.md)

## Restauration géographique

La restauration géographique vous permet de restaurer une base de données SQL sur n’importe quel serveur dans n’importe quelle région Azure à partir de la dernière [sauvegarde quotidienne automatisée](sql-database-automated-backups.md) géo-répliquée. La géo-restauration utilise une sauvegarde géo-redondante en tant que source et peut être mise à profit pour récupérer une base de données même si la base de données ou le centre de données est inaccessible en raison d’une défaillance. Vous pouvez utiliser le [portail Azure](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md) ou [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)

> [AZURE.SELECTOR]
- [Restauration géographique : portail Azure](sql-database-geo-restore-portal.md)
- [Restauration géographique : PowerShell](sql-database-geo-restore-powershell.md)

La géo-restauration constitue l’option de récupération par défaut lorsque votre base de données est indisponible en raison d’un incident dans la région où la base de données est hébergée. Si un incident à grande échelle dans une région entraîne l’indisponibilité de votre application de base de données, vous pouvez utiliser la géo-restauration pour restaurer une base de données à partir de la sauvegarde la plus récente sur un serveur situé dans n’importe quelle autre région. Toutes les sauvegardes sont géo-répliquées et peuvent présenter un délai entre le moment où la sauvegarde est effectuée et celui où elle est géo-répliquée dans l’objet blob Azure dans une autre région. Ce délai peut atteindre une heure. En cas d’incident, il peut donc y avoir jusqu’à une heure de pertes de données, c’est-à-dire que le RPO peut être égal à une heure. L’image ci-dessous illustre la restauration de la base de données à partir de la dernière sauvegarde quotidienne.

![géo-restauration](./media/sql-database-geo-restore/geo-restore-2.png)

Pour plus d’informations sur l’utilisation de la restauration géographique pour la récupération suite à une panne, consultez [Récupération après une panne](sql-database-disaster-recovery.md)

> [AZURE.IMPORTANT] Bien que la géo-restauration soit disponible pour tous les niveaux de service, il s’agit de la solution de récupération d’urgence la plus basique proposée dans la base de données SQL Azure, avec le RPO et le temps de récupération estimé (ERT) les plus longs. Pour les bases de données de base dont la taille ne dépasse pas 2 Go, la géo-restauration offre une solution de récupération d’urgence intéressante avec un ERT de 12 heures. Pour les bases de données Standard ou Premium plus volumineuses, vous pouvez utiliser la géoréplication active pour bénéficier de temps de récupération plus courts ou réduire le risque de perte de données. La géoréplication active offre un RPO et un ERT nettement inférieurs, car elle nécessite simplement l’initialisation d’un basculement vers une base de données secondaire répliquée en continu. Pour plus d’informations, consultez [Géo-réplication active](sql-database-geo-replication-overview.md).

## Exécution par programme d’une récupération à l’aide des sauvegardes automatisées

Comme indiqué plus haut, en plus du portail Azure, la récupération de la base de données peut être effectuée par programme à l’aide d’Azure PowerShell et de l’API REST. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles.

### PowerShell

|Applet de commande|Description|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/fr-FR/library/azure/mt603648.aspx)|Obtient une ou plusieurs bases de données.|
|[Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/fr-FR/library/azure/mt693387.aspx)|Obtient une base de données supprimée que vous pouvez restaurer.|
|[Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx)|Obtient une sauvegarde géo-redondante d’une base de données.|
|[Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx)|Restaure une base de données SQL.|
||||

### API REST

|API|Description|
|---|-----------|
|[REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Restaure une base de données|
|[Créer ou mettre à jour l’état de la base de données](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Retourne l’état durant une opération de restauration|
||||



## Résumé

Les sauvegardes automatiques protègent vos bases de données des erreurs utilisateur et des erreurs d’application, de la suppression accidentelle d’une base de données et des interruptions prolongées. Cette solution gratuite et ne nécessitant aucune gestion est disponible pour toutes les bases de données SQL.

## Étapes suivantes

- Pour une vue d’ensemble de la continuité des activités, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
- Pour en savoir plus sur la conception de la continuité des activités et les scénarios de récupération, consultez [Scénarios de continuité des activités](sql-database-business-continuity-scenarios.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)

<!---HONumber=AcomDC_0629_2016-->