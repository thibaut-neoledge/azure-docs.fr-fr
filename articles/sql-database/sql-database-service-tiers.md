---
title: "Performances de la base de données SQL : niveaux de service | Microsoft Docs"
description: Comparez les niveaux de service SQL Database.
keywords: "options de base de données,performances de base de données"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/15/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 7bbdbe345bd468c01e2a790610bcf6c063c11f9b


---
# <a name="sql-database-service-tiers-for-single-databases-and-elastic-database-pools"></a>Niveaux de service SQL Database pour les bases de données uniques et les pools de bases de données élastiques
[base de données SQL Azure](sql-database-technical-overview.md) propose trois niveaux de service associés à plusieurs niveaux de performance pour gérer différentes charges de travail. Plus un niveau de performances est élevé, plus l’ensemble de ressources dont vous disposez est étendu afin de générer un meilleur débit. Vous pouvez modifier les niveaux de service et de performances de manière dynamique. Pour en savoir plus, voir [Modification des niveaux de service et de performances de base de données](sql-database-scale-up.md) .

Vous pouvez gérer chaque base de données dans son propre [niveau de service](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) avec son propre niveau de performances. Vous pouvez également gérer plusieurs bases de données dans un [pool de bases de données élastique](sql-database-service-tiers.md#elastic-database-pool-service-tiers-and-performance-in-edtus) avec un ensemble partagé de ressources. Les ressources disponibles pour les bases de données uniques sont exprimées en termes d’unités de transaction de base de données (DTU) et pour les pools de bases de données élastiques en termes de DTU ou d’eDTU élastiques. Pour en savoir plus sur les DTU et les eDTU, voir [Qu’est-ce qu’une DTU ?](sql-database-what-is-a-dtu.md). 

Dans les deux cas, les niveaux de service incluent **De base**, **Standard** et **Premium**. 

## <a name="service-tiers"></a>Niveaux de service
Les niveaux de service De base, Standard et Premium proposent un contrat SLA garantissant un temps d'activité de 99,99 %. Par ailleurs, ils proposent des performances prévisibles, des options de continuité d'activité d'entreprise professionnelles, des fonctionnalités de gestion de la sécurité et une facturation à l'heure. Le tableau suivant fournit des exemples de niveaux adaptés à différentes charges de travail d'application.

| Niveau de service | Charges de travail cibles |
| :--- | --- |
| **De base** |Idéal pour une petite base de données prenant en général en charge une seule opération active à la fois. Exemple : bases de données utilisées pour le développement ou le test ou pour des applications à petite échelle rarement utilisées. |
| **Standard** |L'option idéale pour la plupart des applications cloud, prenant en charge plusieurs requêtes simultanées. Exemple : applications Web ou de groupe de travail. |
| **Premium** |Conçu pour un volume transactionnel élevé, prenant en charge un grand nombre d’utilisateurs simultanés et nécessitant le plus haut niveau de continuité d’activité possible. Exemple : bases de données prenant en charge des applications critiques. |

> [!NOTE]
> Les éditions Web et Business ont été retirées. Si vous souhaitez continuer d’utiliser ces éditions, reportez-vous à la section [FAQ Sunset](https://azure.microsoft.com/pricing/details/sql-database/web-business/) .
> 
> 

## <a name="single-database-service-tiers-and-performance-levels"></a>Niveau de service et niveau de performances d’une base de données unique
Il existe plusieurs niveaux de performances au sein de chaque niveau de service pour les bases de données uniques. Vous avez la possibilité de choisir le niveau qui répond le mieux aux besoins de votre charge de travail. Si vous avez besoin de faire monter ou descendre le système en puissance, vous pouvez très rapidement modifier le niveau de performances de votre base de données. Pour en savoir plus, voir [Modification des niveaux de service et de performances de base de données](sql-database-scale-up.md) .

Les caractéristiques de performances répertoriées ici s’appliquent aux bases de données créées à l’aide de [SQL Database V12](sql-database-technical-overview.md). Quel que soit le nombre de bases de données hébergées, votre base de données obtient toujours un ensemble de ressources garanti, et les caractéristiques de performance attendues de la base de données ne sont pas affectées.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Pour une explication détaillée du contenu de toutes les autres lignes de ce tableau de niveaux de service, consultez [Capacités et limites des niveaux de service](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 
> 

## <a name="elastic-database-pool-service-tiers-and-performance-in-edtus"></a>Niveau de service et niveau de performances d’un pool de base de données élastiques dans les eDTU
Vous pouvez également gérer plusieurs bases de données dans un [pool de bases de données élastique](sql-database-elastic-pool.md). Toutes les bases de données dans un pool de bases de données élastique partagent un ensemble commun de ressources. Les caractéristiques de performances sont mesurées par les *unités de transaction de base de données élastique* (eDTU). Comme avec les bases de données uniques, les pools sont fournis en trois niveaux de service : **De base**, **Standard** et **Premium**. Pour les pools, ces trois niveaux de service définissent toujours les limites de performances globales et les fonctionnalités proposées.

Les pools permettent aux bases de données de partager et de consommer des ressources DTU sans avoir à affecter un niveau de performances spécifique à chaque base de données dans le pool. Par exemple, une base de données dans un pool Standard peut être offerte avec de 0 eDTUe au nombre d’eDTU maximal défini lors de la configuration du pool. Les pools permettent à plusieurs bases de données avec différentes charges de travail d’utiliser efficacement les ressources eDTU disponibles pour l’ensemble du pool. Consultez [Considérations sur les prix et performances pour un pool de bases de données élastique](sql-database-elastic-pool-guidance.md) pour plus d’informations.

Le tableau suivant décrit les caractéristiques des pools de base de données élastique De base, Standard et Premium.

[!INCLUDE [SQL DB service tiers table for elastic database pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Chaque base de données au sein d'un pool respecte également les caractéristiques de base de données unique pour ce niveau. Par exemple, le pool de base possède une limite de sessions maximale par pool de 4800 à 28800, mais une base de données individuelle dans un pool de base a une limite de base de données de 300 sessions.

## <a name="choosing-a-service-tier"></a>Choix d’un niveau de service
Pour choisir un niveau de service, commencez par déterminer si la base de données doit être unique ou faire partie d’un pool de bases de données élastique. 

### <a name="choosing-a-service-tier-for-a-single-database"></a>Choix d’un niveau de service pour une base de données unique
Pour choisir un niveau de service pour une base de données unique, commencez par déterminer les caractéristiques de base de données dont vous avez besoin afin de savoir pour quelle édition du service SQL Database opter :

* Taille de la base de données (2 Go maximum pour l’édition De base, 250 Go maximum pour l’édition Standard et 500 Go à 1 To pour l’édition Premium, selon le niveau de performances)
* Période de rétention des sauvegardes de base de données (7 jours pour l’édition De base, 35 jours pour l’édition Standard et 35 jours pour l’édition Premium)

Une fois que vous avez choisi l’édition du service Base de données SQL, vous êtes prêt à déterminer le niveau de performances (nombre de DTU) requis pour la base de données. Vous pouvez l’évaluer grossièrement, puis [augmenter ou réduire dynamiquement l’échelle](sql-database-scale-up.md) en fonction de l’expérience réelle. Vous pouvez également utiliser l’outil [DTU Calculator](http://dtucalculator.azurewebsites.net/) pour estimer le nombre de DTU nécessaire. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>Choix d’un niveau de service pour un pool de bases de données élastique
Pour choisir un niveau de service pour un pool de base de données élastique, commencez par déterminer les caractéristiques de base de données dont vous avez besoin pour votre pool.

* Taille de la base de données (2 Go pour l’édition De base, 250 Go pour l’édition Standard et 500 Go pour l’édition Premium)
* Période de rétention des sauvegardes de base de données (7 jours pour l’édition De base, 35 jours pour l’édition Standard et 35 jours pour l’édition Premium)
* Nombre de bases de données par pool (400 pour l’édition de base, 400 pour l’édition Standard et 50 pour l’édition Premium)
* Espace de stockage maximal par pool (117 Go pour l’édition De base, 1 200 Go pour l’édition Standard et 750 Go pour l’édition Premium)

Une fois que vous avez choisi le niveau de service pour votre pool, vous êtes prêt à déterminer le niveau de performances (nombre d’eDTU) requis pour le pool. Vous pouvez l’évaluer grossièrement, puis [augmenter ou réduire dynamiquement l’échelle](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) en fonction de l’expérience réelle. Vous pouvez utiliser l’outil [DTU calculatrice](http://dtucalculator.azurewebsites.net/) pour estimer le nombre de DTU nécessaire pour chaque base de données d’un pool, afin de définir la limite supérieure pour le pool.

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la tarification de ces niveaux, voir [Base de données SQL - Tarification](https://azure.microsoft.com/pricing/details/sql-database/).
* Découvrez en détail les [pools de bases de données élastiques](sql-database-elastic-pool-guidance.md) et les [considérations sur les prix et performances pour un pool de bases de données élastique](sql-database-elastic-pool-guidance.md).
* Découvrez comment [analyser, gérer et redimensionner des pools de bases de données élastiques](sql-database-elastic-pool-manage-portal.md) et [analyser les performances des bases de données uniques](sql-database-single-database-monitor.md).
* Maintenant que vous êtes au fait des différents niveaux disponibles pour le service Base de données SQL, essayez-les à l’aide d’un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) et découvrez [comment créer votre première base de données SQL](sql-database-get-started.md).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Modèles de conception pour les applications SaaS mutualisées avec Base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Formation vidéo Microsoft Virtual Academy sur les fonctions de bases de données élastiques dans Azure SQL Database](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)




<!--HONumber=Nov16_HO4-->


