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
ms.date: 02/09/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 920143756a0e0b369cf839681f9c4308f77a4af0


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Options et performances de la base de données SQL : comprendre ce qui est disponible dans chaque niveau de service
[Azure SQL Database](sql-database-technical-overview.md) propose trois niveaux de service (**De base**, **Standard** et **Premium**) associés à plusieurs niveaux de performances pour gérer différentes charges de travail. Plus un niveau de performances est élevé, plus le nombre de ressources dont vous disposez est étendu afin de générer un meilleur débit. Vous pouvez modifier les [niveaux de service et de performances de manière dynamique](sql-database-service-tiers.md) sans aucun temps d’arrêt. Les niveaux de service De base, Standard et Premium proposent tous un contrat SLA garantissant un temps d’activité de 99,99 %. Par ailleurs, ils offrent des options de continuité d’activité flexibles, des fonctionnalités de gestion de la sécurité et une facturation à l’heure. 

Vous pouvez créer des bases de données uniques avec des ressources dédiées sur le [niveau de performances](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) sélectionné. Vous pouvez également gérer plusieurs bases de données dans un [pool élastique](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) dans lequel les ressources sont partagées entre les bases de données. Les ressources disponibles pour les bases de données uniques sont exprimées en termes d’unités de transaction de base de données (DTU), et pour les pools élastiques, en termes de DTU élastiques (eDTU). Pour plus d’informations sur les DTU et les eDTU, consultez l’article [Qu’est-ce qu’une DTU ?](sql-database-what-is-a-dtu.md) 

Dans les deux cas, les niveaux de service incluent **De base**, **Standard** et **Premium**. 

## <a name="choosing-a-service-tier"></a>Choix d’un niveau de service
Le tableau suivant fournit des exemples de niveaux adaptés à différentes charges de travail d'application.

| Niveau de service | Charges de travail cibles |
| :--- | --- |
| **De base** | Idéal pour une petite base de données prenant en général en charge une seule opération active à la fois. Exemple : bases de données utilisées pour le développement ou le test ou pour des applications à petite échelle rarement utilisées. |
| **Standard** |Option idéale pour les applications cloud avec des exigences minimes ou moyennes en matières de performances d’E/S, en prenant en charge plusieurs requêtes simultanées. Exemple : applications Web ou de groupe de travail. |
| **Premium** | Option conçue pour un volume transactionnel élevé avec de hautes exigences en matière de performances d’E/S, en prenant en charge un grand nombre d’utilisateurs simultanés. Exemple : bases de données prenant en charge des applications critiques. |

Commencez par déterminer si vous souhaitez exécuter une base de données unique ou regrouper des bases de données partageant des ressources. Consultez les [considérations relatives aux pools élastiques](sql-database-elastic-pool-guidance.md). Pour choisir un niveau de service, commencez par déterminer les fonctionnalités de base de données minimales dont vous avez besoin :

* Taille maximale des différentes bases de données (2 Go pour le niveau De base, 250 Go pour le niveau Standard et 500 Go à 1 To pour le niveau Premium dans le cadre des niveaux de performances élevés)
* Stockage total maximal dans un pool élastique (117 Go pour le niveau De base, 1 200 Go pour le niveau Standard et 750 Go pour le niveau Premium)
* Nombre maximal de bases de données par pool (400 pour le niveau De base, 400 pour le niveau Standard et 50 pour le niveau Premium)
* Période de rétention des sauvegardes de base de données (7 jours pour le niveau De base, 35 jours pour les niveaux Standard et Premium)

Une fois que vous avez déterminé le niveau de service minimal, vous êtes prêt à déterminer le niveau de performances (nombre de DTU) requis pour la base de données. Les niveaux de performances S2 et S3 standard constituent souvent un bon point de départ. Dans le cas des bases de données présentant des exigences élevées en termes de processeur ou d’E/S, les niveaux de performances Premium sont bien adaptés. Le niveau Premium offre davantage de ressources processeur et 10 fois plus d’E/S au départ que le niveau de performances Standard le plus élevé.

## <a name="single-database-service-tiers-and-performance-levels"></a>Niveau de service et niveau de performances d’une base de données unique
Il existe plusieurs niveaux de performances au sein de chaque niveau de service pour les bases de données uniques. Vous avez la possibilité de choisir le niveau qui répond le mieux aux besoins de votre charge de travail. Si vous avez besoin de faire monter ou descendre le système en puissance, vous pouvez très facilement modifier ses niveaux. Pour en savoir plus, voir [Modification des niveaux de service et de performances de base de données](sql-database-service-tiers.md) .

Quel que soit le nombre de bases de données hébergées, votre base de données obtient toujours un ensemble de ressources garanti, et les caractéristiques de performance attendues de la base de données ne sont pas affectées.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Pour une explication détaillée du contenu de toutes les autres lignes de ce tableau de niveaux de service, consultez [Capacités et limites des niveaux de service](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>Mise à l’échelle supérieure ou inférieure d’une base de données unique

Après avoir choisi un niveau de service et un niveau de performances initiaux, vous pouvez mettre à l’échelle supérieure ou inférieure une base de données unique de façon dynamique sur la base de votre expérience concrète. 

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
> Pour la procédure détaillée, consultez [Gestion des bases de données uniques avec le portail Azure](sql-database-manage-single-databases-portal.md), [Gestion des bases de données uniques avec Powershell](sql-database-manage-single-databases-powershell.md) ou [Gestion des bases de données uniques avec Transact-SQL](sql-database-manage-single-databases-tsql.md).
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

> [!IMPORTANT]
> Pour la procédure détaillée, consultez [Gestion des pools élastiques avec le portail Azure](sql-database-elastic-pool-manage-portal.md), [Gestion des pools élastiques avec Powershell](sql-database-elastic-pool-manage-powershell.md), [Gestion des pools élastiques avec Transact-SQL](sql-database-elastic-pool-manage-tsql.md) ou [Gestion des pools élastiques avec C#](sql-database-elastic-pool-manage-csharp.md).
>

## <a name="next-steps"></a>Étapes suivantes

* Découvrez en détail les [pools élastiques](sql-database-elastic-pool-guidance.md) et les [considérations sur les prix et performances pour les pools élastiques](sql-database-elastic-pool-guidance.md).
* Découvrez comment [analyser, gérer et redimensionner des pools élastiques](sql-database-elastic-pool-manage-portal.md) et [analyser les performances des bases de données uniques](sql-database-single-database-monitor.md).
* Maintenant que vous êtes au fait des différents niveaux disponibles pour le service Base de données SQL, essayez-les à l’aide d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) et découvrez [comment créer votre première base de données SQL](sql-database-get-started.md).
* Dans le cas des scénarios de migration, utilisez l’outil [DTU Calculator](http://dtucalculator.azurewebsites.net/) pour estimer le nombre de DTU nécessaire. 




<!--HONumber=Feb17_HO3-->


