<properties
   pageTitle="Découvrir les sauvegardes SQL Database | Microsoft Azure" 
   description="Découvrez les sauvegardes de base de données intégrées de SQL Database qui vous permettent de restaurer une base de données Azure SQL Database à une version antérieure ou de copier une base de données vers une nouvelle base de données dans une région géographique (jusqu’à 35 jours)."
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




<!-- # H1 Title

 H1 title should answer the question "What is in this topic?" Write the title in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  
-->

# <a name="learn-about-sql-database-backups"></a>Découvrir les sauvegardes SQL Database

<!-- Introduction

1. Sentence #1 begins with "Learn about ..." and gives the scope of what the article will cover.
2. Sentence #2 explains the key capability or selling point of the feature.
3. Sentence #3 begins with "Use this feature to ..." and gives a common use case.

-->

SQL Database crée une sauvegarde de base de données locale toutes les cinq minutes et utilise le stockage géoredondant avec accès en lecture (RA-GRS) d’Azure pour copier certaines des sauvegardes de base de données dans une autre région géographique. Ces sauvegardes se font **automatiquement et sans frais supplémentaires**. Utilisez les sauvegardes de base de données locales pour [restaurer une base de données à une version antérieure](sql-database-point-in-time-restore-portal.md) sur le même serveur. Utilisez des sauvegardes géoredondantes pour [restaurer la base de données dans une autre région géographique](sql-database-geo-restore-portal.md).  

>[AZURE.NOTE] Les sauvegardes locales et géoredondantes se font automatiquement. Vous n’avez rien à faire pour qu’elles se produisent et il n’y a aucun frais supplémentaire. 

Dans le diagramme suivant, SQL Database est en cours d’exécution dans la région Est des États-Unis. Il crée une sauvegarde de base de données toutes les cinq minutes, qui est stockée localement dans le stockage géoredondant avec accès en lecture (RA-GRS) d’Azure. Azure copie les sauvegardes de base de données dans un centre de données couplé dans la région Ouest des États-Unis.

![Restauration géographique](./media/sql-database-geo-restore/geo-restore-1.png)

<!--## What is <feature>?" -->

## <a name="what-is-a-sql-database-backup"></a>Qu’est-ce qu’une sauvegarde SQL Database ?  

<!-- 
First sentence begins with "The <feature> is ..." followed by a definition of the feature. Provide a 1-2 paragraph intro to explain what the feature is, how it works, and the importance of the feature for solving business problems.
-->
Une sauvegarde de base de données SQL Database est un fichier qui stocke des informations sur l’état de la base de données à un moment spécifique dans le temps. SQL Database utilise la technologie SQL Server pour créer des sauvegardes locales [complètes](https://msdn.microsoft.com/library/ms186289.aspx), [différentielles](https://msdn.microsoft.com/library/ms175526.aspx ) et du [journal des transactions](https://msdn.microsoft.com/library/ms191429.aspx). Les sauvegardes du journal des transactions se produisent toutes les cinq minutes, ce qui vous permet d’effectuer une restauration à un point dans le temps sur le même serveur que celui qui héberge la base de données. Quand vous restaurez une base de données, le service identifie les sauvegardes nécessitant une restauration (complète, différentielle ou journal des transactions).

>[AZURE.NOTE] SQL Database crée automatiquement des sauvegardes de base de données locales et géoredondantes. Vous n’avez pas besoin de faire quoi que ce soit pour qu’elles se produisent. Il n’y a aucun frais supplémentaire.

Utilisez une sauvegarde de base de données pour :

- Restaure une base de données à un point dans le temps dans la période de rétention. La sauvegarde d’une base de données vous permet de restaurer une base de données à un point dans le temps, de restaurer une base de données supprimée au moment de la suppression ou de restaurer une base de données sur une autre zone géographique. Pour effectuer une restauration, consultez [Restaurer une base de données à partir d’une sauvegarde de base de données](sql-database-recovery-using-backups.md).

- Copiez une base de données sur un serveur SQL Server dans la même région ou dans une autre région. La copie est cohérente au niveau transactionnel avec l’instance SQL actuelle. Pour effectuer une copie, consultez [Copie de base de données](sql-database-copy.md).

- Archivez une sauvegarde de base de données au-delà de la période de rétention des sauvegardes. Pour effectuer un archivage, [exportez une base de données SQL vers un fichier BACPAC](sql-database-export.md). Vous pouvez ensuite archiver le fichier BACPAC sur un stockage à long-terme et le stocker au-delà de votre période de rétention. Sinon, utilisez le fichier BACPAC pour transférer une copie de votre base de données sur SQL Server, localement ou dans une machine virtuelle Azure.

## <a name="backups-have-geographical-redundancy"></a>Les sauvegardes ont une redondance géographique

SQL Database utilise la [réplication Stockage Azure](../storage/storage-redundancy.md) pour sauvegarder votre base de données à un autre emplacement géographique. Pour fournir un stockage géoredondant, SQL Database stocke les fichiers de sauvegarde de base de données locale dans un compte [Stockage géoredondant avec accès en lecture (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage). Azure réplique les fichiers de sauvegarde dans un [centre de données couplé](../best-practices-availability-paired-regions.md). Cette géoréplication vous garantit que vous pouvez restaurer une base de données dans les scénarios où vous ne pouvez pas accéder à la sauvegarde de la base de données à partir de la région de votre base de données primaire. 

>[AZURE.NOTE] Dans le stockage Azure, le terme *réplication* fait référence à la copie de fichier d’un emplacement à un autre. La *réplication de base de données* de SQL fait référence à la gestion de la synchronisation de plusieurs bases de données secondaires avec une base de données primaire. 


## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Quelle est la quantité de stockage de sauvegarde incluse sans frais ?

SQL Database fournit jusqu’à 200 % du stockage de base de données maximal configuré pour le stockage de sauvegarde sans coût supplémentaire. Par exemple, si vous avez une instance de base de données Standard configurée à une taille de 250 Go, vous bénéficiez de 500 Go d’espace de stockage de sauvegarde sans coût supplémentaire. Si votre base de données dépassé la capacité fournie de stockage de sauvegarde, vous pouvez décider de réduire la période de rétention, en contactant le support Azure. Vous pouvez également décider de payer le stockage de sauvegarde supplémentaire, qui est facturé au taux standard de stockage redondant avec accès en lecture. 

## <a name="how-often-do-backups-happen"></a>À quelle fréquence les sauvegardes se produisent-elles ?

Les sauvegardes complètes de base de données sont effectuées chaque semaine, les sauvegardes différentielles de base de données sont effectuées toutes les heures, et les sauvegardes du journal des transactions sont effectuées toutes les 5 minutes. La première sauvegarde complète est planifiée immédiatement après la création d’une base de données. Elle s’exécute généralement en 30 minutes, mais elle peut nécessiter davantage de temps s’il s’agit d’une base de données de taille considérable. Par exemple, la sauvegarde initiale peut prendre davantage de temps sur une base de données restaurée ou une copie de base de donnée. Après la première sauvegarde complète, toutes les sauvegardes sont planifiées automatiquement et gérées en mode silencieux en arrière-plan. La durée exacte des sauvegardes complètes et [différentielles](https://msdn.microsoft.com/library/ms175526.aspx) des base de données est déterminée en fonction de l’équilibrage de la charge de travail globale du système. 

## <a name="how-long-do-you-keep-my-backups"></a>Combien de temps conservez-vous mes sauvegardes ?

Chaque sauvegarde SQL Database a une période de rétention qui est basée sur le [-niveau de service](sql-database-service-tiers.md) de la base de données. Les périodes de rétention pour une base de données sont les suivantes :

- Niveau de service De base : sept jours.
- Niveau de service Standard : 35 jours.
- Niveau de service Premium : 35 jours.


Si vous rétrogradez votre base de données des niveaux de service Standard ou Premium au niveau De base, les sauvegardes sont conservées sept jours. Toutes les sauvegardes existantes d’une ancienneté supérieure à sept jours ne sont plus disponibles. 

Si vous mettez à niveau votre base de données du niveau De base vers le niveau Standard ou Premium, SQL Database conserve les sauvegardes existantes jusqu’à ce que leur ancienneté soit de 35 jours. Il conserve les nouvelles sauvegardes pendant 35 jours.
 
Si vous supprimez une base de données, SQL Database conserve les sauvegardes de la même façon que s’il s’agissait d’une base de données en ligne. Par exemple, supposons que vous supprimez une base de données qui a une période de rétention de sept jours. Une sauvegarde dont l’ancienneté est de quatre jours est conservée trois jours de plus.

> [AZURE.IMPORTANT]Si vous supprimez le serveur SQL Server Azure, toutes les bases de données qui appartiennent au serveur sont également supprimées, sans pouvoir être restaurées. Vous ne pouvez pas restaurer un serveur supprimé.

## <a name="next-steps"></a>Étapes suivantes

Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Pour voir comment les sauvegardes de base de données peuvent s’adapter à votre stratégie d’entreprise, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).





<!--HONumber=Oct16_HO2-->


