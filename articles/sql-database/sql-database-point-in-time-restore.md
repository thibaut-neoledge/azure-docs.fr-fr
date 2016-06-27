<properties
   pageTitle="Continuité d’activité dans le Cloud - Limite de restauration dans le temps - Base de données SQL | Microsoft Azure"
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
   ms.workload="data-management"
   ms.date="05/10/2016"
   ms.author="sstein"/>

# Vue d’ensemble : limite de restauration dans le temps d’une base de données SQL

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-point-in-time-restore.md)
- [Portail Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

La limite de restauration dans le temps vous permet de restaurer votre base de données à un point antérieur dans le temps à l’aide de [sauvegardes automatisées de la base de données SQL](sql-database-automated-backups.md). Vous pouvez restaurer à un point antérieur dans le temps à l’aide du [portail Azure](sql-database-point-in-time-restore-portal.md), de [PowerShell](sql-database-point-in-time-restore-powershell.md) ou [de l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

La base de données peut être restaurée vers n’importe quel niveau de performance ou pool élastique. Vous devez vous assurer de disposer d’un quota DTU suffisant sur le serveur ou le pool. N’oubliez pas que la restauration crée une nouvelle base de données, et que les performances et le niveau de service de la base de données restaurée peuvent être différents de ceux de la base de données d’origine. Une fois le processus terminé, la base de données restaurée est une base de données normale entièrement accessible en ligne, facturée aux tarifs habituels en fonction de ses performances et de son niveau de service. Si vous restaurez la base de données à des fins de récupération, vous pouvez traiter la base de données restaurée comme remplacement de la base de données d’origine, ou l’utiliser pour en extraire des données afin de mettre à jour la base de données d’origine. Si la base de données restaurée est destinée à remplacer la base de données d’origine, vous devez vérifier que les performances et/ou de niveau de service sont appropriés, et effectuer une mise à l’échelle si nécessaire. Vous pouvez renommer la base de données d’origine, puis donner le nom d’origine à la base de données restaurée à l’aide de la commande ALTER DATABASE dans T-SQL. Si vous voulez récupérer des données à partir de la base de données restaurée, vous devrez écrire et exécuter séparément les scripts de récupération de données dont vous avez besoin. Bien que l’opération de restauration puisse prendre un certain temps, la base de données sera visible dans la liste de base de données pendant tout le processus. Si vous supprimez la base de données pendant la restauration, cela annule l’opération et vous ne serez pas facturé.

## Durée de la récupération via la limite de restauration dans le temps

Le temps nécessaire pour restaurer une base de données dépend de nombreux facteurs, notamment de la taille de la base de données, du point de restauration sélectionné et de la quantité d’activités devant être relues pour reconstruire l’état au point sélectionné. Pour une base de données de très grande taille et/ou très active, la restauration peut prendre plusieurs heures. La restauration d’une base de données crée systématiquement une nouvelle base de données sur le même serveur que la base de données d’origine. La base de données restaurée doit donc être renommée.

## Sauvegarde/restauration ou copie/exportation/importation

La limite de restauration dans le temps est l’approche recommandée pour la récupération des bases de données de base, standard et premium suite à une perte ou altération accidentelle des données. La sauvegarde et la restauration sont moins coûteuses (il n’y a aucun frais pour les sauvegardes sauf si elles sont excessives, alors que vous êtes facturé pour la copie d’une base de données nécessaire pour assurer une exportation transactionnelle cohérente et pour stocker le fichier BACPAC), ne nécessitent aucune gestion (les sauvegardes sont automatiques, alors que vous devez gérer ou planifier les exportations vous-même), et offrent un meilleur RPO (vous pouvez restaurer à un point spécifique dans le temps avec une granularité beaucoup plus fine (une minute) qu’il n’est possible avec une copie/exportation/importation) et un meilleur temps de récupération (la restauration à partir de sauvegardes est généralement plus rapide que l’importation, qui implique la création d’un schéma, la désactivation des index, le chargement des données, puis l’activation individuelle des index pour chaque table). Sachez que la copie et l’exportation restent la solution recommandée pour l’archivage à long terme, au-delà de la période de rétention.


## Résumé

Les sauvegardes automatiques et la limite de restauration dans le temps protègent vos bases de données contre l’altération ou la suppression accidentelle des données. Cette solution gratuite et ne nécessitant aucune gestion est disponible pour toutes les bases de données SQL. La sauvegarde et la restauration représentent une amélioration significative par rapport à la solution copie/exportation/importation pour les besoins de récupération à court terme. Nous vous conseillons d’utiliser la limite de restauration dans le temps dans le cadre de votre stratégie de continuité et de n’utiliser l’exportation qu’en cas de besoins d’archivage à long terme ou à des fins de migration de données.


## Étapes suivantes

- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)
- [Limite de restauration dans le temps à l’aide du portail Azure](sql-database-point-in-time-restore-portal.md)
- [Limite de restauration dans le temps à l’aide de](sql-database-point-in-time-restore-powershell.md)
- [Limite de restauration dans le temps à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)


## Ressources supplémentaires

- [restauration d’une base de données supprimée.](sql-database-restore-deleted-database.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->