<properties
   pageTitle="Continuité des affaires cloud - Sauvegarde intégrée - Base de données | Microsoft Azure"
   description="Découvrez-en plus sur les sauvegardes intégrées SQL Database qui vous permettent de restaurer Azure SQL Database à une version antérieure ou de copier une base de données vers une nouvelle base de données dans une zone géographique (jusqu’à 35 jours)."
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
   ms.workload="sqldb-features"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Présentation : sauvegardes automatisées d’une base de données SQL

Le service de base de données SQL Azure protège toutes les bases de données grâce à une sauvegarde automatisée conservée pendant 7 jours en mode de base, 14 jours en mode standard et 35 jours en mode premium. Vous pouvez utiliser ces sauvegardes automatisées pour effectuer des restaurations dans le temps et pour restaurer une base de données supprimée après une altération ou suppression accidentelle de données.

Les sauvegardes de base de données sont effectuées automatiquement sans avoir à activer quoi que ce soit, et n’entraînent aucuns frais supplémentaires. Ces sauvegardes automatisées et la limite de restauration dans le temps permettent de protéger vos bases de données sans frais et sans administration contre les altérations ou suppressions accidentelles, quelle qu’en soit la cause.

## Coûts de la sauvegarde automatisée

Microsoft Azure SQL Database fournit jusqu’à 200 % du stockage de base de données maximal configuré pour le stockage de sauvegarde sans coût supplémentaire. Par exemple, si vous avez une instance de base de données Standard configurée à une taille de 250 Go, vous bénéficiez de 500 Go d’espace de stockage de sauvegarde sans coût supplémentaire. Si votre base de données dépasse l’espace de stockage de sauvegarde fourni, vous pouvez choisir de réduire la période de rétention en contactant le Support technique Azure ou en achetant l’espace de stockage supplémentaire facturé au tarif RA-GRS (Read-Access Geo Redundant Microsoft Azure) standard.

## Détails de la sauvegarde automatique

Toutes les bases de données de base, standard et premium sont protégées par des sauvegardes automatiques. Des sauvegardes complètes sont effectuées chaque semaine, des sauvegardes différentielles tous les jours, et des sauvegardes de journal toutes les 5 minutes. La première sauvegarde complète est planifiée immédiatement après la création d’une base de données. Le processus dure normalement 30 minutes, mais peut prendre plus de temps. Si une base de données est déjà volumineuse, par exemple si elle a été créée en copiant ou restaurant une base de données volumineuse, la première sauvegarde complète peut prendre plus de temps. Après la première sauvegarde complète, toutes les sauvegardes sont planifiées automatiquement et gérées en mode silencieux en arrière-plan. La programmation exacte des sauvegardes complètes et différentielles est déterminée par le système, afin d’équilibrer la charge globale. Les fichiers de sauvegarde sont stockés dans un compte de stockage géo-redondant avec accès en lecture (RA-GRS) pour garantir la disponibilité à des fins de récupération d’urgence.

## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->