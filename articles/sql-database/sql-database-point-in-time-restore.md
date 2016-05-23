<properties
   pageTitle="Continuité d’activité dans le Cloud - Limite de restauration dans le temps | Microsoft Azure"
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

La limite de restauration dans le temps vous permet de restaurer votre base de données à un point antérieur dans le temps.

Le service de base de données SQL Azure protège toutes les bases de données grâce à une sauvegarde automatisée conservée pendant 7 jours en mode de base, 14 jours en mode standard et 35 jours en mode premium. La limite de restauration dans le temps est conçue pour vous permettre de récupérer votre base de données à partir d’une sauvegarde suite à une altération ou suppression accidentelle des données.

Les sauvegardes de base de données sont effectuées automatiquement sans avoir à activer quoi que ce soit, et n’entraînent aucuns frais supplémentaires. Ces sauvegardes automatisées et la limite de restauration dans le temps permettent de protéger vos bases de données sans frais et sans administration contre les altérations ou suppressions accidentelles, quelle qu’en soit la cause.


|Tâche (portail) | PowerShell | REST |
|:--|:--|:--|
| [Restaurer une base de données SQL à un état antérieur](sql-database-point-in-time-restore-portal.md) | [PowerShell](sql-database-point-in-time-restore-powershell.md) | [REST (createMode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |
| [Restaurer une base de données SQL supprimée](sql-database-restore-deleted-database-portal.md) | [PowerShell](sql-database-restore-deleted-database-powershell.md) | [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|



## Présentation des sauvegardes automatiques

Toutes les bases de données de base, standard et premium sont protégées par des sauvegardes automatiques. Des sauvegardes complètes sont effectuées chaque semaine, des sauvegardes différentielles tous les jours, et des sauvegardes de journal toutes les 5 minutes. La première sauvegarde complète est planifiée immédiatement après la création d’une base de données. Le processus dure normalement 30 minutes, mais peut prendre plus de temps. Si une base de données est déjà volumineuse, par exemple si elle a été créée en copiant ou restaurant une base de données volumineuse, la première sauvegarde complète peut prendre plus de temps. Après la première sauvegarde complète, toutes les sauvegardes sont planifiées automatiquement et gérées en mode silencieux en arrière-plan. La programmation exacte des sauvegardes complètes et différentielles est déterminée par le système, afin d’équilibrer la charge globale. Les fichiers de sauvegarde sont stockés dans un compte de stockage géo-redondant avec accès en lecture (RA-GRS) pour garantir la disponibilité à des fins de récupération d’urgence.


## Durée de la récupération via la limite de restauration dans le temps

Le temps nécessaire pour restaurer une base de données dépend de nombreux facteurs, notamment de la taille de la base de données, du point de restauration sélectionné et de la quantité d’activités devant être relues pour reconstruire l’état au point sélectionné. Pour une base de données de très grande taille et/ou très active, la restauration peut prendre plusieurs heures. La restauration d’une base de données crée systématiquement une nouvelle base de données sur le même serveur que la base de données d’origine. La base de données restaurée doit donc être renommée.


La base de données peut être restaurée vers n’importe quel niveau de performance ou pool élastique. Vous devez vous assurer de disposer d’un quota DTU suffisant sur le serveur ou le pool. N’oubliez pas que la restauration crée une nouvelle base de données, et que les performances et le niveau de service de la base de données restaurée peuvent être différents de ceux de la base de données d’origine. Une fois le processus terminé, la base de données restaurée est une base de données normale entièrement accessible en ligne, facturée aux tarifs habituels en fonction de ses performances et de son niveau de service. Si vous restaurez la base de données à des fins de récupération, vous pouvez traiter la base de données restaurée comme remplacement de la base de données d’origine, ou l’utiliser pour en extraire des données afin de mettre à jour la base de données d’origine. Si la base de données restaurée est destinée à remplacer la base de données d’origine, vous devez vérifier que les performances et/ou de niveau de service sont appropriés, et effectuer une mise à l’échelle si nécessaire. Vous pouvez renommer la base de données d’origine, puis donner le nom d’origine à la base de données restaurée à l’aide de la commande ALTER DATABASE dans T-SQL. Si vous voulez récupérer des données à partir de la base de données restaurée, vous devrez écrire et exécuter séparément les scripts de récupération de données dont vous avez besoin. Bien que l’opération de restauration puisse prendre un certain temps, la base de données sera visible dans la liste de base de données pendant tout le processus. Si vous supprimez la base de données pendant la restauration, cela annule l’opération et vous ne serez pas facturé.



## Restauration d’une base de données récemment supprimée

Si vous supprimez une base de données de base, standard ou premium, la dernière sauvegarde est conservée pendant la période de rétention normale, ce qui vous permet de restaurer la base de données au point auquel elle a été supprimée.

Pour les bases de données supprimées, le point de restauration est fixé à l’heure de suppression de la base de données. Et, comme précédemment, lorsque vous restaurez une base de données supprimée, elle peut uniquement être restaurée sur le serveur contenant la base de données d’origine. Gardez cela à l’esprit lorsque vous supprimez un serveur, car une fois le serveur supprimé, vous ne serez pas en mesure de restaurer les bases de données qui y étaient stockées.

## Sauvegarde/restauration ou copie/exportation/importation

La limite de restauration dans le temps est l’approche recommandée pour la récupération des bases de données de base, standard et premium suite à une perte ou altération accidentelle des données. La sauvegarde et la restauration sont moins coûteuses (il n’y a aucun frais pour les sauvegardes sauf si elles sont excessives, alors que vous êtes facturé pour la copie d’une base de données nécessaire pour assurer une exportation transactionnelle cohérente et pour stocker le fichier BACPAC), ne nécessitent aucune gestion (les sauvegardes sont automatiques, alors que vous devez gérer ou planifier les exportations vous-même), et offrent un meilleur RPO (vous pouvez restaurer à un point spécifique dans le temps avec une granularité beaucoup plus fine (une minute) qu’il n’est possible avec une copie/exportation/importation) et un meilleur temps de récupération (la restauration à partir de sauvegardes est généralement plus rapide que l’importation, qui implique la création d’un schéma, la désactivation des index, le chargement des données, puis l’activation individuelle des index pour chaque table). Sachez que la copie et l’exportation restent la solution recommandée pour l’archivage à long terme, au-delà de la période de rétention.


## Résumé

Les sauvegardes automatiques et la limite de restauration dans le temps protègent vos bases de données contre l’altération ou la suppression accidentelle des données. Cette solution gratuite et ne nécessitant aucune gestion est disponible pour toutes les bases de données SQL. La sauvegarde et la restauration représentent une amélioration significative par rapport à la solution copie/exportation/importation pour les besoins de récupération à court terme. Nous vous conseillons d’utiliser la limite de restauration dans le temps dans le cadre de votre stratégie de continuité et de n’utiliser l’exportation qu’en cas de besoins d’archivage à long terme ou à des fins de migration de données.



## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0511_2016-->