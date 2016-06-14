<properties
   pageTitle="Évolutivité des performances pour l’Azure SQL Data Warehouse | Microsoft Azure"
   description="Comprendre les capacités de montée en puissance des performances dans l’Azure SQL Data Warehouse. Montez en puissance en ajustant le nombre d’unités DWU ou suspendez et reprenez des ressources de calcul pour réduire les coûts."
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
   ms.date="05/26/2016"
   ms.author="barbkess;sonyama"/>

# Évolutivité des performances pour l’Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-overview-scalability.md)
- [Portail](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

L’architecture de SQL Data Warehouse sépare le stockage du calcul, ce qui permet de les mettre à l’échelle indépendamment l’un de l’autre. Par conséquent, vous pouvez effectuer une montée en charge des performances tout en réduisant les coûts en payant uniquement pour les performances lorsque vous en avez besoin.

Cette présentation décrit les fonctionnalités de montée en puissance parallèle des performances suivantes de SQL Data Warehouse et donne des recommandations sur la manière de les utiliser et quand.

- Mise à l’échelle des performances en ajustant les unités DWU
- Suspension ou reprise de ressources de calcul

<a name="scale-performance-bk"></a>

## Mise à l’échelle des performances

Dans SQL Data Warehouse, vous pouvez rapidement mettre les performances à l’échelle en augmentant ou diminuant les ressources de calcul du processeur, de la mémoire et de la bande passante d’E/S. Pour mettre les performances à l’échelle, vous devez simplement ajuster le nombre de data warehouse units (DWU) que SQL Data Warehouse alloue à votre base de données. SQL Data Warehouse apporte les modifications rapidement et traite toutes les modifications matérielles ou logicielles sous-jacentes.

>[AZURE.NOTE] L’époque où vous deviez identifier les types de processeurs, le volume de mémoire et le type de stockage nécessaires à la satisfaction des besoins en performances de votre entrepôt de données est désormais révolue. En plaçant votre solution Data Warehouse dans le cloud, vous éliminez les problématiques matérielles de niveau inférieur. Aujourd'hui, SQL Data Warehouse vous pose cette question : À quelle vitesse souhaitez-vous analyser vos données ?

### Qu’est-ce qu’une DWU ?

Une *unité d’entrepôt de données* (data warehouse unit, DWU) est une mesure des capacités de calcul sous-jacentes pour votre base de données à un moment donné. À mesure que le nombre d’unités DWU augmente, SQL Data Warehouse exécute des opérations en parallèle (chargement de données ou requête, par exemple) sur un plus grand nombre de ressources de processeur et de mémoire distribuées. Cela réduit la latence et améliore les performances.

Les unités DWU sont basées sur la charge et les taux d’analyse. Lorsque vous augmentez le nombre d’unités DWU, vous augmentez les taux de charge et d’analyse

- **Taux de charge**. Le nombre d’enregistrements que SQL Data Warehouse peut recevoir par seconde. Plus précisément, il s’agit du nombre d’enregistrements que SQL Data Warehouse peut importer depuis le stockage d’objets blob (Blob Storage) Azure dans un index columnstore en cluster à l’aide de PolyBase. 

- **Taux d’analyse**. Le nombre d’enregistrements qu’une requête peut récupérer depuis SQL Data Warehouse par seconde. Plus précisément, il s’agit du nombre d’enregistrements que SQL Data Warehouse peut renvoyer en exécutant une requête d’entreposage de données dans un index columnstore en cluster. Pour tester le taux d’analyse, nous utilisons une requête d'entreposage de données standard qui analyse un grand nombre de lignes avant d'effectuer une agrégation complexe. Cette opération est très gourmande en E/S et en ressources de processeur.

>[AZURE.NOTE] Nous mettons actuellement en œuvre certaines améliorations importantes en matière de performances et partagerons bientôt les taux attendus. Durant la phase préliminaire, nous apporterons des améliorations continues (par exemple, l’augmentation de la compression et la mise en cache) afin d’augmenter ces taux et de garantir leur mise à l’échelle de façon prévisible.

Pour obtenir une liste des unités DWU, consultez les objectifs de niveau de service dans l’article [Limites de capacité][].

### Comment mettre les performances à l’échelle ?

Pour mettre augmenter ou diminuer votre puissance de calcul de manière élastique, modifiez simplement le paramètre Data Warehouse Units (DWU) pour votre base de données. En arrière-plan, SQL Data Warehouse interrompt votre instance, modifie les affectations de processeur et de mémoire, puis redémarre votre instance.

Les unités DWU sont allouées par blocs de 100, mais tous les blocs ne sont pas disponibles. Les performances augmentent proportionnellement à mesure que le nombre d’unités DWU augmente. Dans des niveaux de DWU plus élevés, vous devez ajouter plus de 100 DWU pour détecter une amélioration significative des performances. Pour vous permettre de sélectionner des sauts significatifs dans le nombre d’unités DWU, nous proposons les niveaux DWU qui donnent les meilleurs résultats.
 
Pour ajuster les unités DWU, vous pouvez utiliser l’une des différentes méthodes suivantes.

- [Mise à l’échelle des performances avec le portail Azure][]
- [Mise à l’échelle des performances avec PowerShell][]
- [Mise à l’échelle des performances avec les API REST][]
- [Mise à l’échelle des performances avec TSQL][]

### Combien d’unités DWU dois-je utiliser ?
 
Les performances de SQL Data Warehouse sont mises à l’échelle de manière linéaire et le passage entre deux capacités données (par exemple de 100 DWU à 2 000 DWU) s’effectue en quelques secondes. Cela vous donne la possibilité de tester différents paramètres de DWU jusqu’à ce que vous déterminiez ceux qui s’adaptent le mieux à votre situation.

> [AZURE.NOTE] Vous n’obtiendrez peut-être pas les performances attendues avec une mise à l’échelle à des volumes de données inférieurs. Nous vous recommandons de commencer par des volumes de données d’au moins 1 To afin d’obtenir des résultats de tests de performances précis.

Recommandations pour rechercher l’unité DWU la mieux adaptée à votre charge de travail :

1. Si vous disposez d’un entrepôt de données en développement, commencez par sélectionner un nombre réduit d’unités DWU.
2. Surveillez les performances de votre application, en observant notamment le nombre d’unités DWU sélectionné.
3. Déterminez le niveau de performances le mieux adapté aux exigences en modulant la capacité de votre système à l’aide d’une mise à l’échelle linéaire.
4. Augmentez ou diminuez le nombre de DWU en fonction de la performance de charge de travail dont vous avez besoin. Le service répond rapidement et ajuste le volume de ressources de calcul en fonction des exigences des nouvelles unités DWU.
5. Continuez à effectuer des ajustements jusqu’à ce que vous atteigniez le niveau de performances requis par vos activités.

### Quand dois-je mettre les unités DWU à l’échelle ?

En général, nous tenons à simplifier au maximum les DWU. Lorsque vous avez besoin de résultats plus rapides, augmentez vos DWU et payez simplement un peu plus cher pour obtenir de meilleures performances. Lorsque vous avez besoin d’une puissance de traitement moindre, réduisez vos DWU et payez uniquement ce dont vous avez besoin.

Recommandations sur le moment approprié pour mettre des unités DWU à l’échelle :

1. Si votre application présente une charge de travail fluctuante, augmentez et réduisez le nombre d’unités DWU en fonction les pics et les creux. Par exemple, si votre charge de travail atteint habituellement un pic à la fin du mois, envisagez d’ajouter davantage d’unités DWU durant ces quelques jours, avant de réduire l’échelle une fois la période d’activité intense passée.
1. Avant d’exécuter une opération de chargement ou de transformation de données importante, vous pouvez augmenter le nombre d’unités DWU afin que vos données soient disponibles plus rapidement.

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
Consultez les articles suivants afin de mieux comprendre certains concepts supplémentaires clés de performance et de mise à l’échelle :

- [modèle concurrentiel][]
- [tables de conception][]
- [associer une clé de distribution par hachage à votre table][]
- [statistiques pour améliorer les performances][]

<!--Image reference-->

<!--Article references-->

[Mise à l’échelle des performances avec le portail Azure]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-1-scale-performance
[Mise à l’échelle des performances avec PowerShell]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-1-scale-performance
[Mise à l’échelle des performances avec les API REST]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-1-scale-performance
[Mise à l’échelle des performances avec TSQL]: ./sql-data-warehouse-manage-scale-out-tasks-tsql.md

[Limites de capacité]: ./sql-data-warehouse-service-capacity-limits.md

[Suspension du calcul avec le portail Azure]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-2-pause-compute
[Suspension du calcul avec PowerShell]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-2-pause-compute
[Suspension du calcul avec des API REST]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-2-pause-compute
[Reprise du calcul avec le portail Azure]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-3-resume-compute
[Reprise du calcul avec PowerShell]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-3-resume-compute
[Reprise du calcul avec des API REST]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-3-resume-compute

[modèle concurrentiel]: ./sql-data-warehouse-develop-concurrency.md
[tables de conception]: ./sql-data-warehouse-develop-table-design.md
[associer une clé de distribution par hachage à votre table]: ./sql-data-warehouse-develop-hash-distribution-key.md
[statistiques pour améliorer les performances]: ./sql-data-warehouse-develop-statistics.md
[development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0601_2016-->