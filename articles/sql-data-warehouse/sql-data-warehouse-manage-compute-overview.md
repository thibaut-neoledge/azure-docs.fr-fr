<properties
   pageTitle="Gestion de la puissance de calcul dans Azure SQL Data Warehouse (Vue d’ensemble) | Microsoft Azure"
   description="Capacités de montée en puissance des performances dans Azure SQL Data Warehouse. Montez en puissance en ajustant le nombre d’unités DWU ou suspendez et reprenez des ressources de calcul pour réduire les coûts."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/01/2016"
   ms.author="barbkess;sonyama"/>

# Gestion de la puissance de calcul dans Azure SQL Data Warehouse (Vue d’ensemble)

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-manage-compute-overview.md)
- [Portail](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

L’architecture de SQL Data Warehouse sépare le stockage du calcul, ce qui permet de les mettre à l’échelle indépendamment l’un de l’autre. Par conséquent, vous pouvez effectuer une montée en charge des performances tout en réduisant les coûts en payant uniquement pour les performances lorsque vous en avez besoin.

Cette présentation décrit les fonctionnalités de montée en puissance parallèle des performances suivantes de SQL Data Warehouse et donne des recommandations sur la manière de les utiliser et quand.

- Mise à l’échelle de la puissance de calcul en ajustant les unités [DWU][]
- Suspension ou reprise de ressources de calcul

<a name="scale-performance-bk"></a>

## Mise à l’échelle des performances

Dans SQL Data Warehouse, vous pouvez rapidement mettre les performances à l’échelle en augmentant ou diminuant les ressources de calcul du processeur, de la mémoire et de la bande passante d’E/S. Pour mettre les performances à l’échelle, vous devez simplement ajuster le nombre de [data warehouse units (DWU)][] que SQL Data Warehouse alloue à votre base de données. SQL Data Warehouse apporte les modifications rapidement et traite toutes les modifications matérielles ou logicielles sous-jacentes.

L’époque où vous deviez identifier les types de processeurs, le volume de mémoire et le type de stockage nécessaires à la satisfaction des besoins en performances de votre entrepôt de données est désormais révolue. En plaçant votre solution Data Warehouse dans le cloud, vous éliminez les problématiques matérielles de niveau inférieur. Aujourd'hui, SQL Data Warehouse vous pose cette question : À quelle vitesse souhaitez-vous analyser vos données ?

### Comment mettre les performances à l’échelle ?

Pour augmenter ou diminuer votre puissance de calcul de manière élastique, modifiez simplement le paramètre [Data Warehouse Units (DWU)][] pour votre base de données. Augmentation des performances linéairement lorsque vous ajoutez plus de DWU. Dans des niveaux de DWU plus élevés, vous devez ajouter plus de 100 DWU pour détecter une amélioration significative des performances. Pour vous permettre de sélectionner des sauts significatifs dans le nombre d’unités DWU, nous proposons les niveaux DWU qui donnent les meilleurs résultats.
 
Pour ajuster les unités DWU, vous pouvez utiliser l’une des différentes méthodes suivantes.

- [Mise à l’échelle de la puissance de calcul avec le portail Azure][]
- [Mise à l’échelle de la puissance de calcul avec PowerShell][]
- [Mise à l’échelle de la puissance de calcul avec les API REST][]
- [Mise à l’échelle de la puissance de calcul avec TSQL][]

### Combien d’unités DWU dois-je utiliser ?
 
Les performances de SQL Data Warehouse sont mises à l’échelle de manière linéaire et le passage entre deux capacités données (par exemple de 100 DWU à 2 000 DWU) s’effectue en quelques secondes. Cela vous donne la possibilité de tester différents paramètres de DWU jusqu’à ce que vous déterminiez ceux qui s’adaptent le mieux à votre situation.

Pour obtenir votre valeur DWU idéale, essayez d’augmenter et de réduire vos DWU et d’exécuter quelques requêtes après le chargement de vos données. Puisque la mise à l’échelle est rapide, vous pouvez essayer plusieurs niveaux différents de performances en une heure ou moins. Gardez à l’esprit que SQL Data Warehouse est conçu pour traiter de grandes quantités de données. Pour apprécier ses véritables capacités de mise à l’échelle, en particulier à plus grande échelle, vous voudrez utiliser un jeu de données volumineux approchant ou dépassant les 1 To.

Recommandations pour rechercher l’unité DWU la mieux adaptée à votre charge de travail :

1. Si vous disposez d’un entrepôt de données en développement, commencez par sélectionner un nombre réduit d’unités DWU. DW400 ou DW200 est un bon point de départ.
2. Surveillez les performances de votre application, en observant notamment le nombre d’unités DWU sélectionné.
3. Déterminez le niveau de performances le mieux adapté aux exigences en modulant la capacité de votre système à l’aide d’une mise à l’échelle linéaire.
4. Augmentez ou diminuez le nombre de DWU en fonction de la performance de charge de travail dont vous avez besoin. Le service répond rapidement et ajuste le volume de ressources de calcul en fonction des exigences des nouvelles unités DWU.
5. Continuez à effectuer des ajustements jusqu’à ce que vous atteigniez le niveau de performances requis par vos activités.

### Quand dois-je mettre les unités DWU à l’échelle ?

Lorsque vous avez besoin de résultats plus rapides, augmentez vos DWU et payez simplement un peu plus cher pour obtenir de meilleures performances. Lorsque vous avez besoin d’une puissance de traitement moindre, réduisez vos DWU et payez uniquement ce dont vous avez besoin.

Recommandations sur le moment approprié pour mettre des unités DWU à l’échelle :

1. Si votre application présente une charge de travail fluctuante, augmentez et réduisez le nombre d’unités DWU en fonction les pics et les creux. Par exemple, si votre charge de travail atteint habituellement un pic à la fin du mois, envisagez d’ajouter davantage d’unités DWU durant ces quelques jours, avant de réduire l’échelle une fois la période d’activité intense passée.
2. Avant d’exécuter une opération de chargement ou de transformation de données importante, vous pouvez augmenter le nombre d’unités DWU afin que vos données soient disponibles plus rapidement.

<a name="pause-compute-bk"></a>

## Suspension du calcul

[AZURE.INCLUDE [Description de la suspension de SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Pour suspendre une base de données, utilisez l’une des différentes méthodes suivantes.

- [Suspension du calcul avec le portail Azure][]
- [Suspension du calcul avec PowerShell][]
- [Suspension du calcul avec des API REST][]

<a name="resume-compute-bk"></a>

## Reprise du calcul

[AZURE.INCLUDE [Description de la reprise de SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]

Pour reprendre une base de données, utilisez l’une des différentes méthodes suivantes.

- [Reprise du calcul avec le portail Azure][]
- [Reprise du calcul avec PowerShell][]
- [Reprise du calcul avec des API REST][]

<a name="next-steps-bk"></a>

## Étapes suivantes
Consultez les articles suivants afin de mieux comprendre certains concepts supplémentaires clés de performance :

- [Gestion des charges de travail et d’accès concurrentiel][]
- [Vue d’ensemble de conception de table][]
- [Distribution de table][]
- [Indexation de table][]
- [Partitionnement de table][]
- [Statistiques de table][]
- [Meilleures pratiques][]

<!--Image reference-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[data warehouse units (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Mise à l’échelle de la puissance de calcul avec le portail Azure]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[Mise à l’échelle de la puissance de calcul avec PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Mise à l’échelle de la puissance de calcul avec les API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Mise à l’échelle de la puissance de calcul avec TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Suspension du calcul avec le portail Azure]: ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Suspension du calcul avec PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Suspension du calcul avec des API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Reprise du calcul avec le portail Azure]: ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Reprise du calcul avec PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Reprise du calcul avec des API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Gestion des charges de travail et d’accès concurrentiel]: ./sql-data-warehouse-develop-concurrency.md
[Vue d’ensemble de conception de table]: ./sql-data-warehouse-tables-overview.md
[Distribution de table]: ./sql-data-warehouse-tables-distribute.md
[Indexation de table]: ./sql-data-warehouse-tables-index.md
[Partitionnement de table]: ./sql-data-warehouse-tables-partition.md
[Statistiques de table]: ./sql-data-warehouse-tables-statistics.md
[Meilleures pratiques]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0706_2016-->