<properties
   pageTitle="Continuité des affaires cloud - Sauvegarde intégrée - Base de données SQL | Microsoft Azure"
   description="Découvrez-en plus sur les sauvegardes intégrées SQL Database qui vous permettent de restaurer Azure SQL Database à une version antérieure ou de copier une base de données vers une nouvelle base de données dans une zone géographique (jusqu’à 35 jours)."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# Sauvegardes automatisées d’une base de données SQL

Le service de base de données SQL Azure protège toutes les bases de données grâce à une sauvegarde automatisée conservée pendant 7 jours en mode de base, 35 jours en mode standard et 35 jours en mode premium. Consultez la page [Niveaux de service](sql-database-service-tiers.md) pour plus d’informations sur les fonctionnalités disponibles avec chaque niveau de service.

Les sauvegardes de base de données sont effectuées automatiquement sans avoir à activer quoi que ce soit, et n’entraînent aucuns frais supplémentaires. Ces sauvegardes automatisées et la limite de restauration dans le temps permettent de protéger vos bases de données sans frais et sans administration contre les altérations ou suppressions accidentelles, quelle qu’en soit la cause. Vous pouvez utiliser ces sauvegardes automatisées pour effectuer des restaurations dans le temps et pour restaurer une base de données supprimée après une altération ou suppression accidentelle de données.

## Coûts de la sauvegarde automatisée

Microsoft Azure SQL Database fournit jusqu’à 200 % du stockage de base de données maximal configuré pour le stockage de sauvegarde sans coût supplémentaire. Par exemple, si vous avez une instance de base de données Standard configurée à une taille de 250 Go, vous bénéficiez de 500 Go d’espace de stockage de sauvegarde sans coût supplémentaire. Si votre base de données dépasse l’espace de stockage de sauvegarde fourni, vous pouvez choisir de réduire la période de rétention en contactant le Support technique Azure ou en achetant l’espace de stockage supplémentaire facturé au tarif RA-GRS (Read-Access Geo Redundant Microsoft Azure) standard.

## Détails de la sauvegarde automatique

Toutes les bases de données de base, standard et premium sont protégées par des sauvegardes automatiques. Les sauvegardes complètes de base de données sont effectuées chaque semaine, les sauvegardes différentielles de base de données sont effectuées toutes les heures, et les sauvegardes du journal des transactions sont effectuées toutes les 5 minutes. La première sauvegarde complète est planifiée immédiatement après la création d’une base de données. Le processus dure normalement 30 minutes, mais peut prendre plus de temps. Si une base de données est déjà volumineuse, par exemple si elle a été créée en copiant ou restaurant une base de données volumineuse, la première sauvegarde complète peut prendre plus de temps. Après la première sauvegarde complète, toutes les sauvegardes sont planifiées automatiquement et gérées en mode silencieux en arrière-plan. La programmation exacte des sauvegardes complètes et différentielles est déterminée par le système, afin d’équilibrer la charge globale.

## Géo-redondance

Les fichiers de sauvegarde sont stockés dans un compte de stockage géo-redondant avec accès en lecture (RA-GRS) pour garantir la disponibilité à des fins de récupération d’urgence. Cela garantit la réplication des fichiers de sauvegarde sur un [centre de données jumelé](../best-practices-availability-paired-regions.md). Les sections suivantes montrent la géoréplication de sauvegardes hebdomadaires et quotidiennes stockées dans un compte de stockage géo-redondant avec accès en lecture (RA-GRS) pour garantir la disponibilité à des fins de récupération d’urgence.

![géo-restauration](./media/sql-database-geo-restore/geo-restore-1.png)

## Utilisation de sauvegardes automatisées

Vous pouvez [restaurer une base de données à partir de sauvegardes automatisées](sql-database-recovery-using-backups.md) lors de leur [période de rétention](sql-database-service-tiers.md) vers :

- Une nouvelle base de données sur le même serveur logique récupéré à un point spécifié dans le temps durant la période de rétention.
- Une base de données sur le même serveur logique récupéré à l’heure de suppression pour une base de données supprimée.
- Une nouvelle base de données sur un serveur logique dans n’importe quelle région récupérée sur les sauvegardes quotidiennes plus récentes dans le stockage d’objets blob géo-répliqué (RA-GRS).

Vous pouvez également utiliser les [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md) pour créer une [copie de base de données](sql-database-copy.md) sur n’importe quel serveur logique dans n’importe quelle région qui est cohérente au niveau transactionnel avec la base de données SQL actuelle. Vous pouvez utiliser la copie de base de données et [l’exporter vers un fichier BACPAC](sql-database-export.md) pour archiver une copie cohérente au niveau transactionnel d’une base de données pour le stockage à long terme au-delà de la période de rétention, ou pour transférer une copie de votre base de données vers une instance de machine virtuelle Azure ou locale de SQL Server.

## Que se passe-t-il pour la période de rétention du point de restauration lorsque je mets à niveau vers une version antérieure/supérieure par niveau de service ?

Après la mise à niveau vers un niveau de performances inférieur, la période de rétention du point de restauration est immédiatement tronquée en fonction de la période de rétention du niveau de performances de la base de données actuelle. Si le niveau de service de la base de données est mis à niveau, la période de rétention s'étendra uniquement après la mise à niveau de la base de données. Par exemple, si la base de données est mise à niveau vers la version De base, la période de rétention passera immédiatement de 35 jours à 7 jours. Tous les points de restauration antérieurs à 35 jours ne seront plus disponibles. Par la suite, si la base de données est remise à niveau vers Standard ou Premium, la période de rétention commencera à 7 jours et s’étendra jusqu’à 35 jours.

## Quelle est la durée d'une période de rétention pour une base de données supprimée ? 
La période de rétention est déterminée par le niveau de service de la base de données au moment de son existence ou le nombre de jours pendant laquelle la base de données existe, quel que soit le chiffre inférieur.

> [AZURE.IMPORTANT] Si vous supprimez une instance de serveur de base de données SQL Azure, toutes ses bases de données sont également supprimées et ne peuvent pas être récupérées. Il n'existe aucune prise en charge pour la restauration d'un serveur supprimé pour l'instant.

## Étapes suivantes

- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)
- Pour une vue d’ensemble de la continuité des activités, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)

<!---HONumber=AcomDC_0907_2016-->