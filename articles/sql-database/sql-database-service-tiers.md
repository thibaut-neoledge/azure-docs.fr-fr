---
title: "Performances de la base de données SQL : niveaux de service | Microsoft Docs"
description: Comparez les niveaux de service SQL Database.
keywords: "options de base de données,performances de base de données"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
wms.date: 03/06/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 4307797b3961d8efef4045590e340268f0ad226d
ms.lasthandoff: 03/10/2017


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Options et performances de la base de données SQL : comprendre ce qui est disponible dans chaque niveau de service

[Azure SQL Database](sql-database-technical-overview.md) propose quatre niveaux de service : **De base**, **Standard**, **Premium** et **Premium RS**. Chaque niveau de service est associé à plusieurs niveaux de performances pour gérer différentes charges de travail. Plus un niveau de performances est élevé, plus le nombre de ressources dont vous disposez est étendu afin de générer un meilleur débit. Vous pouvez modifier les niveaux de service et de performances de manière dynamique sans aucun temps d’arrêt. Les niveaux de service De base, Standard et Premium proposent tous un contrat SLA garantissant un temps d’activité de 99,99 %. Par ailleurs, ils offrent des options de continuité d’activité flexibles, des fonctionnalités de gestion de la sécurité et une facturation à l’heure. Le niveau Premium RS fournit les mêmes niveaux de performances, fonctionnalités de sécurité et fonctionnalités de continuité de l’activité que le niveau Premium, avec un SLA (contrat de niveau de service) réduit.

> [!IMPORTANT]
> Les bases de données Premium RS s’exécutent avec un nombre inférieur de copies redondantes par rapport aux bases de données Premium ou Standard. Par conséquent, en cas d’échec d’un service, vous devrez peut-être restaurer votre base de données à partir d’une sauvegarde avec un délai allant jusqu’à 5 minutes.
>

Vous pouvez créer des bases de données uniques avec des ressources dédiées au sein d’un niveau de service avec un [niveau de performances](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) spécifique. Vous pouvez également créer des bases de données dans un [pool élastique](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) dans lequel les ressources sont partagées entre plusieurs bases de données. Les ressources disponibles pour les bases de données uniques sont exprimées en termes d’unités de transaction de base de données (DTU) et pour les pools élastiques en termes d’unités de transaction de base de données élastique (eDTU). Pour plus d’informations sur les DTU et les eDTU, consultez l’article [Qu’est-ce qu’une DTU ?](sql-database-what-is-a-dtu.md) 

## <a name="choosing-a-service-tier"></a>Choix d’un niveau de service
Le tableau suivant fournit des exemples de niveaux adaptés à différentes charges de travail d'application.

| Niveau de service | Charges de travail cibles |
| :--- | --- |
| **De base** | Idéal pour une petite base de données prenant en général en charge une seule opération active à la fois. Exemple : bases de données utilisées pour le développement ou le test ou pour des applications à petite échelle rarement utilisées. |
| **Standard** |Option idéale pour les applications cloud avec des exigences minimes ou moyennes en matières de performances d’E/S, en prenant en charge plusieurs requêtes simultanées. Exemple : applications Web ou de groupe de travail. |
| **Premium** | Option conçue pour un volume transactionnel élevé avec de hautes exigences en matière de performances d’E/S, en prenant en charge un grand nombre d’utilisateurs simultanés. Exemple : bases de données prenant en charge des applications critiques. |
| **Premium RS** | Conçu pour les charges de travail intensives en E/S qui ne nécessitent pas de garanties de disponibilité maximale. Parmi les exemples, on peut citer les charges de travail hautes performances ou une charge de travail analytique où la base de données n’est pas le système d’enregistrement. |
|||

Tout d’abord, vous devez décider si vous souhaitez exécuter une base de données unique avec une quantité définie de ressources dédiées ou si vous souhaitez partager un pool de ressources dans un groupe de bases de données. Consultez les [considérations relatives aux pools élastiques](sql-database-elastic-pool-guidance.md). Pour choisir un niveau de service, commencez par déterminer les fonctionnalités de base de données minimales dont vous avez besoin :

| **Fonctionnalités de niveau de service** | **De base** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Taille maximale de base de données individuelle | 2 Go | 250 Go | 4 To*  | 500 Go  |
| Stockage total maximum dans un pool élastique | 117 Go | 1200 Go | 750 Go | 750 Go |
| Nombre maximal de bases de données par pool | 400  | 400 | 50 | 50 |
| Période de rétention de sauvegarde de bases de données | 7 jours | 35 jours | 35 jours | 35 jours |
||||||

> [!IMPORTANT]
> Les clients utilisant les niveaux de performances P11 et P15 peuvent utiliser jusqu’à 4 To de stockage inclus sans frais supplémentaires. Cette option de 4 To est actuellement en version préliminaire publique dans les régions suivantes : Est des États-Unis 2, États-Unis de l’Ouest, Europe de l’Ouest, Asie du Sud-Est, Japon de l’Est, Est de l’Australie, Centre du Canada et Est du Canada. Pour connaître les limitations actuelles, consultez [Limitations 4 To actuelles](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

Une fois que vous avez déterminé le niveau de service minimal, vous êtes prêt à déterminer le niveau de performances (nombre de DTU) requis pour la base de données. Les niveaux de performances S2 et S3 standard constituent souvent un bon point de départ. Dans le cas des bases de données présentant des exigences élevées en termes de processeur ou d’E/S, les niveaux de performances Premium sont bien adaptés. Le niveau Premium offre davantage de ressources processeur et 10 fois plus d’E/S au départ que le niveau de performances Standard le plus élevé.

## <a name="single-database-service-tiers-and-performance-levels"></a>Niveau de service et niveau de performances d’une base de données unique
Il existe plusieurs niveaux de performances au sein de chaque niveau de service pour les bases de données uniques. Vous avez la possibilité de choisir le niveau le plus adapté aux exigences de votre charge de travail en utilisant le [portail Azure](sql-database-manage-single-databases-portal.md), [PowerShell](sql-database-manage-single-databases-powershell.md), [Transact-SQL](sql-database-manage-single-databases-tsql.md), C# et l’API REST. 

Quel que soit le nombre de bases de données hébergées, votre base de données obtient toujours un ensemble de ressources garanti, et les caractéristiques de performance attendues de la base de données ne sont pas affectées.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Pour une explication détaillée du contenu de toutes les autres lignes de ce tableau de niveaux de service, consultez [Capacités et limites des niveaux de service](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>Mise à l’échelle supérieure ou inférieure d’une base de données unique

Après avoir choisi un niveau de service et un niveau de performances initiaux, vous pouvez mettre à l’échelle supérieure ou inférieure une base de données unique de façon dynamique sur la base de votre expérience concrète. Si vous avez besoin de faire monter le système en puissance ou de réduire son échelle, vous pouvez très simplement modifier ses niveaux dans le [portail Azure](sql-database-manage-single-databases-portal.md), [PowerShell](sql-database-manage-single-databases-powershell.md), [Transact-SQL](sql-database-manage-single-databases-tsql.md), C# et l’API REST. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

La modification du niveau de service et/ou de performances d’une base de données crée un réplica de la base de données d’origine au nouveau niveau de performances, puis bascule les connexions vers ce réplica. Aucune donnée n’est perdue lors de ce processus, mais pendant le bref instant où nous basculons vers le réplica, les connexions à la base de données sont désactivées, de sorte que certaines transactions en cours sont susceptibles d’être restaurées. Cette fenêtre de désactivation varie, mais dure moins de 4 secondes en moyenne, et ne dépasse pas 30 secondes dans plus de 99 % des cas. Cette durée peut se révéler supérieure, en particulier s’il existe un très grand nombre de transactions en cours au moment où les connexions sont désactivées.  

La durée de la totalité du processus de montée en puissance dépend de la taille et du niveau de service de la base de données avant et après la modification. Par exemple, le basculement d’une base de données de 250 Go vers, depuis ou dans un niveau de service Standard ne demande pas plus de 6 heures. Le changement des niveaux de performances d’une base de données de la même taille dans le niveau de service Premium doit s’effectuer en moins de 3 heures.

* Pour qu’une base de données puisse passer à une version antérieure, sa taille doit être inférieure à la taille maximale autorisée par le niveau de service voulu. 
* Lors de la mise à niveau d’une base de données avec la [géo-réplication](sql-database-geo-replication-portal.md) activée, vous devez commencer par mettre à niveau les bases de données secondaires associées vers le niveau de performances souhaité avant de procéder à la mise à niveau de la base de données primaire.
* Avant d’effectuer le passage à une version antérieure depuis un niveau de service Premium, vous devez arrêter toutes les relations de géo-réplication. Vous pouvez suivre la procédure décrite à la section [Récupération suite à une indisponibilité de service](sql-database-disaster-recovery.md) pour arrêter le processus de réplication entre la base de données principale et les bases de données secondaires actives.
* Les offres de service de restauration sont différentes selon les niveaux de service. Si vous passez à une version antérieure, vous risquez de ne plus pouvoir effectuer de restauration à un moment donné, ou de bénéficier d’une période de rétention des sauvegardes moins étendue. Pour en savoir plus, voir [Sauvegarde et restauration de base de données SQL Azure](sql-database-business-continuity.md).
* Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.

> [!IMPORTANT]
> Pour la procédure détaillée, consultez [Gérer une base de données unique avec le portail Azure](sql-database-manage-single-databases-portal.md), [Gérer une base de données unique avec PowerShell](sql-database-manage-single-databases-powershell.md) ou [Gérer une base de données unique avec Transact-SQL](sql-database-manage-single-databases-tsql.md).
>

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Niveaux de service d’un pool élastique et performances dans les eDTU.

Les pools permettent aux bases de données de partager et de consommer des ressources eDTU sans qu’il soit nécessaire d’affecter un niveau de performances propre à chaque base de données dans le pool. Par exemple, une base de données unique dans un pool standard peut être offerte avec de 0 eDTUe au nombre d’eDTU maximal défini lors de la configuration du pool. Les pools permettent à plusieurs bases de données avec différentes charges de travail d’utiliser efficacement les ressources eDTU disponibles pour l’ensemble du pool. Consultez l’article [Considérations sur les prix et performances pour un pool élastique](sql-database-elastic-pool-guidance.md) .

Le tableau suivant décrit les caractéristiques des niveaux de service de pool.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Chaque base de données au sein d'un pool respecte également les caractéristiques de base de données unique pour ce niveau. Par exemple, le pool de base possède une limite de sessions maximale par pool de 4800 à 28800, mais une base de données individuelle dans un pool de base a une limite de base de données de 300 sessions.

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>Mise à l’échelle supérieure ou inférieure d’un pool élastique

Après avoir choisi un niveau de service et un niveau de performances initiaux, vous pouvez mettre à l’échelle supérieure ou inférieure le pool élastique de façon dynamique sur la base de votre expérience concrète. 

* En général, le processus de modification du nombre minimal d’eDTU par base de données ou du nombre maximal d’eDTU par base de données prend cinq minutes au maximum.
* La durée de modification de la taille du pool (eDTU) dépend de la taille combinée de toutes les bases de données du pool. Ce processus prend en moyenne 90 minutes au maximum, pour chaque tranche de 100 Go. Par exemple, si l’espace total de toutes les bases de données du pool est égal à 200 Go, une opération de modification du nombre d’eDTU par pool prend 3 heures au maximum.

Pour la procédure détaillée, consultez [Gérer un pool élastique dans le portail Azure](sql-database-elastic-pool-manage-portal.md), [Gérer un pool élastique avec PowerShell](sql-database-elastic-pool-manage-powershell.md), [Gérer un pool élastique avec Transact-SQL](sql-database-elastic-pool-manage-tsql.md) ou [Gérer un pool élastique avec C#](sql-database-elastic-pool-manage-csharp.md).

## <a name="creating-or-upgrading-to-4tb"></a>Création ou mise à niveau vers 4 To

Les sections suivantes présentent les détails d’implémentation pour l’option de 4 To.

### <a name="creating-in-the-azure-portal"></a>Création dans le portail Azure

Lorsque vous créez une base de données P11/P15, l’option de stockage par défaut de 1 To est présélectionnée. Pour les bases de données situées dans l’une des régions prises en charge, vous pouvez augmenter le stockage maximal à 4 To. Pour toutes les autres régions, le stockage ne peut pas être modifié. Le prix ne change pas lorsque vous sélectionnez l’option de 4 To de stockage inclus.

### <a name="creating-using-powershell-or-transact-sql"></a>Création à l’aide de PowerShell ou de Transact-SQ

Lorsque vous créez une base de données P11/P15, vous pouvez définir la valeur de taille maximale sur 1 To (valeur par défaut) ou sur 4 To. Les valeurs « 1024 Go » et « 4096 Go » sont également acceptées. Si vous choisissez l’option de taille maximale de 4 To, la commande de création échoue avec une erreur si la base de données est configurée dans une région non prise en charge.

### <a name="upgrading-to-4tb"></a>Mise à niveau vers 4 To 

Pour les bases de données P11/P15 existantes situées dans l’une des régions prises en charge, vous pouvez augmenter la taille maximale du stockage à 4 To. Pour ce faire, utilisez le portail Azure, PowerShell ou Transact-SQL. L’exemple suivant illustre la modification du paramètre de taille maximale (maxsize) à l’aide de la commande ALTER DATABASE :

```ALTER DATABASE <DatabaseName> MODIFY (MAXSIZE = 4096 GB);
```

Upgrading an existing P11 or P15 database can only be performed by a server-level principal login or by members of the dbmanager database role. 
If executed in a supported region the configuration will be updated immediately. This can be checked using the [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) or by inspecting the database size in the Azure portal. The database will remain online during the upgrade process. However, you will not be able to utilize the full 4 TB of storage until the actual database files have been upgraded to the new maxsize. The length of time required depends upon on the size of the database being upgraded.  

### Error messages
When creating or upgrading an P11/P15 database in an unsupported region, the create or upgrade operation will fail with the following error message: **P11 and P15 database with up to 4TB of storage are available in US East 2, West US, South East Asia, West Europe, Canada East, Canada Central, Japan East, and Australia East.**

## Current limitations of P11 and P15 databases with 4 TB maxsize

- When creating or updating a P11 or P15 database, you can only chose between 1 TB and 4 TB maxsize. Intermediate storage sizes are not currently supported.
- The 4 TB database maxsize cannot be changed to 1 TB even if the actual storage used is below 1 TB. Thus, you cannot downgrade a P11-4TB/P15-4TB to a P11-1TB/P15-1TB or a lower performance tier (e.g., to P1-P6) until we are providing additional storage options for the rest of the performance tiers. This restriction also applies to the restore and copy scenarios including point-in-time, geo-restore, long-term-backup-retention, and database copy. Once a database is configured with the 4 TB option, all restore operations of this database must be into a P11/P15 with 4 TB maxsize.
- For Active Geo-Replication scenarios:
   - Setting up a geo-replication relationship: If the primary database is P11 or P15, the secondary(ies) must also be P11 or P15; lower performance tiers will be rejected as secondaries since they are not capable of supporting 4 TB.
   - Upgrading the primary database in a geo-replication relationship: Changing the maxsize to 4 TB on a primary database will trigger the same change on the secondary database. Both upgrades must be successful for the change on the primary to take effect. Region limitations for the 4TB option apply (see above). If the secondary is in a region that does not support 4 TB, the primary will not be upgraded.
- Using the Import/Export service for loading P11-4TB/P15-4TB databases is not supported. Use SqlPackage.exe to [import](sql-database-import-sqlpackage.md) and [export](sql-database-export-sqlpackage.md) data.

## Next steps

* Learn the details of [elastic pools](sql-database-elastic-pool-guidance.md) and [price and performance considerations for elastic pools](sql-database-elastic-pool-guidance.md).
* Learn how to [Monitor, manage, and resize elastic pools](sql-database-elastic-pool-manage-portal.md) and [Monitor the performance of single databases](sql-database-single-database-monitor.md).
* Now that you know about the SQL Database tiers, try them out with a [free account](https://azure.microsoft.com/pricing/free-trial/) and learn [how to create your first SQL database](sql-database-get-started.md).
* For migration scenarios, use the [DTU Calculator](http://dtucalculator.azurewebsites.net/) to approximate the number of DTUs needed. 


