<properties
   pageTitle="Sauvegarde SQL Database | Microsoft Azure"
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
   ms.date="09/26/2016"
   ms.author="carlrab"/>

# Sauvegardes SQL Database

SQL Database crée automatiquement des sauvegardes de base de données dans le cadre de l’offre de service, sans nécessité d’inscription ni coût supplémentaire. Utilisez les sauvegardes de base de données pour restaurer une base de données à un point antérieur dans le temps. Cet article explique les spécificités d’une fonctionnalité de sauvegarde de base de données dans SQL Database.

## Qu’est-ce qu’une sauvegarde de base de données ?  

Une sauvegarde de base de données est un fichier qui stocke des informations sur l’état de la base de données à un point spécifique dans le temps. SQL Database crée des sauvegardes [complètes](https://msdn.microsoft.com/library/ms186289.aspx), [différentielles](https://msdn.microsoft.com/library/ms175526.aspx) et des [journaux de transactions](https://msdn.microsoft.com/library/ms191429.aspx). Lorsque vous restaurez une base de données à un point dans le temps, le service identifie les sauvegardes nécessitant une restauration.

Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Pour plus d’informations, consultez la page [Vue d’ensemble de la continuité de l’activité avec la base de données Azure SQL](sql-database-business-continuity.md).

## Stockage géo-redondant

Le service SQL Database stocke les fichiers de sauvegarde de base de données dans un compte de stockage géoredondant avec un accès en lecture (RA-GRS). La fonctionnalité RA-GRS de Microsoft Azure Storage réplique les fichiers de sauvegarde sur un [centre de données associé](../best-practices-availability-paired-regions.md). Cette géoréplication vous garantit que vous pouvez restaurer une base de données dans les scénarios où vous ne pouvez pas accéder à la sauvegarde de la base de données à partir de la région de votre base de données primaire. Dans l’exemple suivant, SQL Database crée des sauvegardes de base de données dans la région est des États-Unis et les stocke dans un compte RA-GRS. Ensuite, Microsoft Azure Storage géo-réplique les sauvegardes sur un centre de données associé dans la région ouest des États-Unis.

![géo-restauration](./media/sql-database-geo-restore/geo-restore-1.png)

>[AZURE.NOTE] Dans le stockage Azure, le terme *réplication* fait référence à la copie de fichier d’un emplacement à un autre. La *réplication de base de données* de SQL fait référence à la gestion de la synchronisation de plusieurs bases de données secondaires avec une base de données primaire.

Pour en savoir plus sur :
- Le stockage géoredondant, consultez la section [Réplication Azure Storage](../storage/storage-redundancy.md).
- Le stockage RA-GRS, consultez la section [Stockage géo-redondant avec accès en lecture](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## Coûts de la sauvegarde de base de données

Microsoft Azure SQL Database fournit jusqu’à 200 % du stockage de base de données maximal configuré pour le stockage de sauvegarde sans coût supplémentaire. Par exemple, si vous avez une instance de base de données Standard configurée à une taille de 250 Go, vous bénéficiez de 500 Go d’espace de stockage de sauvegarde sans coût supplémentaire. Si votre base de données dépassé la capacité fournie de stockage de sauvegarde, vous pouvez décider de réduire la période de rétention, en contactant le support Azure. Vous pouvez également décider de payer le stockage de sauvegarde supplémentaire, qui est facturé au taux standard de stockage redondant avec accès en lecture.

## Planifier la sauvegarde de base de données

Toutes les bases de données de base, standard et premium sont protégées par des sauvegardes automatiques. Les sauvegardes complètes de base de données sont effectuées chaque semaine, les sauvegardes différentielles de base de données sont effectuées toutes les heures, et les sauvegardes du journal des transactions sont effectuées toutes les 5 minutes. La première sauvegarde complète est planifiée immédiatement après la création d’une base de données. Elle s’exécute généralement en 30 minutes, mais elle peut nécessiter davantage de temps s’il s’agit d’une base de données de taille considérable. Par exemple, la sauvegarde initiale peut prendre davantage de temps sur une base de données restaurée ou une copie de base de donnée. Après la première sauvegarde complète, toutes les sauvegardes sont planifiées automatiquement et gérées en mode silencieux en arrière-plan. La durée exacte des sauvegardes complètes et [différentielles](https://msdn.microsoft.com/library/ms175526.aspx) des base de données est déterminée en fonction de l’équilibrage de la charge de travail globale du système.

## Période de rétention de sauvegarde de bases de données

Chaque sauvegarde SQL Database est conservée pendant 7 jours pour le niveau De base, 35 jours pour le niveau Standard et 35 jours pour le niveau Premium. Pour plus d’informations sur les fonctions disponibles avec chaque niveau de service, consultez la page [Niveaux de service](sql-database-service-tiers.md).

### Que se passe-t-il pour la période de rétention du point de restauration lorsque je mets à niveau vers une version antérieure/supérieure par niveau de service ?

Après la mise à niveau vers un niveau de performances inférieur, la période de rétention du point de restauration est immédiatement tronquée en fonction de la période de rétention du niveau de performances de la base de données actuelle. Si le niveau de service de la base de données est mis à niveau, la période de rétention s'étendra uniquement après la mise à niveau de la base de données. Par exemple, si une base de données repasse au niveau De base, la période de rétention passe de 35 jours à 7 jours. Immédiatement, tous les points de restauration antérieurs à 7 jours cessent d’être disponibles. Lorsque vous mettez à niveau une base de données Standard ou Premium, la période de rétention est dans un premier temps de 7 jours, puis augmente jusqu’à atteindre 35 jours.

### Quelle est la durée d'une période de rétention pour une base de données supprimée ? 

La période de rétention est déterminée par le niveau de service de la base de données au moment de son existence ou le nombre de jours pendant laquelle la base de données existe, quel que soit le chiffre inférieur.

> [AZURE.IMPORTANT] Si vous supprimez une instance de serveur Microsoft Azure SQL Database, toutes les bases de données qui appartiennent à l’instance sont également supprimées, sans pouvoir être restaurées. Vous ne pouvez pas restaurer un serveur supprimé.


## Utilisations courantes des sauvegardes de bases de données

La vocation principale des sauvegardes de base de données est de restaurer les bases de données à un point dans le temps de la période de rétention. La sauvegarde d’une base de données vous permet de restaurer une base de données à un point dans le temps, de restaurer une base de données supprimée au moment de la suppression ou de restaurer une base de données sur une autre zone géographique.

- Pour en savoir plus sur la restauration de base de données, consultez la section [Récupérer une base de données SQL Azure à l’aide des sauvegardes automatisées d’une base de données](sql-database-recovery-using-backups.md).

Vous pouvez utiliser une sauvegarde de base de données pour copier une base de données sur un serveur SQL logique de toute zone géographique. La copie est cohérente au niveau transactionnel avec l’instance SQL actuelle.

- Pour plus d’informations sur la copie d’une base de données, consultez la section [Copie de base de données](sql-database-copy.md).

Vous pouvez également archiver des sauvegardes automatisées au-delà de la période de rétention, en créant une copie de base de données que vous [exportez vers un fichier BACPAC](sql-database-export.md). Une fois que vous avez le fichier BACPAC, vous pouvez l’archiver sur un espace de stockage de long-terme et le stocker au-delà de votre période de rétention. Sinon, utilisez le fichier BACPAC pour transférer une copie de votre base de données sur SQL Server, localement ou dans une machine virtuelle Azure.

- Pour plus d’informations sur l’archivage de base de données, consultez la section [Copie de base de données](sql-database-copy.md).


## Rubriques connexes

### Scénarios

- Pour une vue d’ensemble de la continuité des activités, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)

### Caractéristiques

Pour en savoir plus sur :

- La restauration de la sauvegarde d’une base de données, consultez la section [Récupérer une base de données SQL Azure à l’aide des sauvegardes automatisées d’une base de données](sql-database-recovery-using-backups.md).
- L’archivage d’une base de données, consultez la section [Copie de base de données](sql-database-copy.md).
- Les options de restauration plus rapides, consultez la section [Vue d’ensemble : Géo-réplication active de base de données SQL](sql-database-geo-replication-overview.md).

<!-- ### Tasks -->

<!-- ### Tutorials -->

<!---HONumber=AcomDC_0928_2016-->