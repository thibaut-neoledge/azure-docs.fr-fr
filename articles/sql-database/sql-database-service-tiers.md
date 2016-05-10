<properties
	pageTitle="Options et performances de la base de données SQL : Niveaux de service | Microsoft Azure"
	description="Comparez les performances et les fonctionnalités de continuité d’activité des différents niveaux de service des bases de données SQL afin de trouver le juste équilibre entre les coûts et les capacités en matière d’évolutivité."
	keywords="options de base de données,performances de base de données"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor="jeffreyg"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="04/27/2016"
	ms.author="carlrab"/>

# Options et performances de la base de données SQL : comprendre ce qui est disponible dans chaque niveau de service

La [base de données SQL Azure](sql-database-technical-overview.md) dispose de plusieurs niveaux de service pour gérer les différentes charges de travail. Vous pouvez modifier les niveaux de service à tout moment sans interrompre le fonctionnement de votre application. Vous pouvez également [créer une base de données unique](sql-database-get-started.md) avec des caractéristiques et une tarification définies. Vous pouvez également gérer plusieurs bases de données en [créant un pool de bases de données élastiques](sql-database-elastic-pool-create-portal.md). Dans les deux cas, les niveaux **De base**, **Standard** et **Premium** sont inclus. Les options de base de données de ces niveaux sont similaires pour les bases de données uniques et les pools élastiques. Toutefois, d’autres considérations sont à prendre en compte pour les pools élastiques. Cet article donne des détails sur les niveaux de service des bases de données uniques et élastiques.

## Niveaux de service et options de base de données
Les niveaux de service De base, Standard et Premium proposent un contrat SLA garantissant un temps d'activité de 99,99 %. Par ailleurs, ils proposent des performances prévisibles, des options de continuité d'activité d'entreprise professionnelles, des fonctionnalités de gestion de la sécurité et une facturation à l'heure. Le tableau suivant fournit des exemples de niveaux adaptés à différentes charges de travail d'application.

| Niveau de service | Charges de travail cibles |
|---|---|
| **De base** | Idéal pour une petite base de données prenant en général en charge une seule opération active à la fois. Exemple : bases de données utilisées pour le développement ou le test ou pour des applications à petite échelle rarement utilisées. |
| **Standard** | L'option idéale pour la plupart des applications cloud, prenant en charge plusieurs requêtes simultanées. Exemple : applications Web ou de groupe de travail. |
| **Premium** | Conçu pour un volume transactionnel élevé, prenant en charge un grand nombre d'utilisateurs simultanés et nécessitant le plus haut niveau de continuité d'activité possible. Exemple : bases de données prenant en charge des applications critiques. |

>[AZURE.NOTE] Les éditions Web et Business ont été retirées. Si vous souhaitez continuer d’utiliser ces éditions, reportez-vous à la section [FAQ Sunset](https://azure.microsoft.com/pricing/details/sql-database/web-business/).

## Niveau de service et niveau de performances d’une base de données unique
Il existe plusieurs niveaux de performances au sein de chaque niveau de service pour les bases de données uniques. Vous avez la possibilité de choisir le niveau qui répond le mieux aux besoins de votre charge de travail. Si vous avez besoin de faire monter le système en puissance ou de réduire son échelle, vous pouvez très simplement modifier ses niveaux **sans occasionner de coupure de service pour votre application.** Pour en savoir plus, voir [Modification des niveaux de service et de performances de base de données](sql-database-scale-up.md).

Les caractéristiques de performances répertoriées ici s’appliquent aux bases de données créées à l’aide de [SQL Database V12](sql-database-v12-whats-new.md). Lorsque le matériel sous-jacent d’Azure héberge plusieurs bases de données SQL, votre base de données obtient toujours un ensemble de ressources garanti. Les caractéristiques de performance attendues de la base de données ne sont pas affectées.

[AZURE.INCLUDE [Tableau de niveaux de service de base de données SQL](../../includes/sql-database-service-tiers-table.md)]

Pour une meilleure compréhension des DTU, consultez la [section DTU](#understanding-dtus) de cette rubrique.

>[AZURE.NOTE] Pour une explication détaillée du contenu de toutes les autres lignes de ce tableau de niveaux de service, consultez [Capacités et limites des niveaux de service](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## Niveaux de service d’un pool élastique et performances dans les eDTU.
Outre la création et la mise à l’échelle d’une base de données unique, vous avez également la possibilité de gérer plusieurs bases de données dans un [pool élastique](sql-database-elastic-pool.md). Toutes les bases de données dans un pool élastique partagent un ensemble commun de ressources. Les caractéristiques de performances sont mesurées par les *unités de transaction de base de données élastique* (eDTU). Comme avec les bases de données uniques, les pools sont fournis en trois niveaux de service : **De base**, **Standard** et **Premium**. Pour les pools, ces trois niveaux de service définissent toujours les limites de performances globales et les fonctionnalités proposées.

Les pools permettent aux bases de données élastiques de partager et de consommer des ressources DTU sans avoir à affecter un niveau de performances spécifique aux bases de données dans le pool. Par exemple, une base de données unique dans un pool standard peut être offerte avec de 0 eDTUe au nombre d’eDTU maximal défini lors de la configuration du pool. Cela permet à plusieurs bases de données avec différentes charges de travail d'utiliser efficacement les ressources eDTU disponibles pour l'ensemble du pool. Consultez l’article [Considérations sur les prix et performances pour un pool élastique](sql-database-elastic-pool-guidance.md).

Le tableau suivant décrit les caractéristiques des niveaux de service de pool.

[AZURE.INCLUDE [Tableau des niveaux de service de base de données SQL pour les bases de données élastiques](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Chaque base de données au sein d'un pool respecte également les caractéristiques de base de données unique pour ce niveau. Par exemple, le pool de base possède une limite de sessions maximale par pool de 2400 à 28800, mais une base de données individuelle dans ce pool a une limite de base de données de 300 sessions (limite pour une seule base de données de base comme spécifié dans la section précédente).

## Présentation des DTU

[AZURE.INCLUDE [Description de la DTU d'une base de données SQL](../../includes/sql-database-understanding-dtus.md)]

## Étapes suivantes
- Pour en savoir plus sur la tarification de ces niveaux, voir [Base de données SQL - Tarification](https://azure.microsoft.com/pricing/details/sql-database/).
- Découvrez les détails des [pools de base de données élastique](sql-database-elastic-pool-guidance.md) et les [considérations sur les prix et performances pour un pool de base de données élastique](sql-database-elastic-pool-guidance.md).
- Découvrez comment [analyser, gérer et redimensionner des pools élastiques](sql-database-elastic-pool-manage-portal.md) et [analyser les performances des bases de données uniques](sql-database-single-database-monitor.md).
- Maintenant que vous êtes au fait des différents niveaux de base de données SQL, essayez-les, grâce à notre [compte gratuit](https://azure.microsoft.com/pricing/free-trial/), et découvrez [comment créer votre première base de données SQL](sql-database-get-started.md).

<!---HONumber=AcomDC_0504_2016-->