<properties
pageTitle="Gestion des tâches d’évolutivité de l’Azure SQL Data Warehouse (TSQL) | Microsoft Azure"
   description="Tâches TSQL permettant une montée en charge des performances de l’Azure SQL Data Warehouse. Utilisez TSQL pour modifier des ressources de calcul en ajustant les unités DWU"
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
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Gestion des tâches d’évolutivité de l’Azure SQL Data Warehouse (TSQL)

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-overview-scalability.md)
- [Portail](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)


Mettez à l'échelle de façon élastique les ressources de calcul et la mémoire pour répondre aux besoins changeants de votre charge de travail, et réduisez vos coûts en réduisant les ressources pendant les heures creuses.

Cette collection de tâches utilise TSQL pour :

- Afficher les paramètres d’unités DWU actuels
- Modifier les ressources de calcul en ajustant les unités DWU

Pour suspendre ou reprendre une base de données, choisissez l’une des autres options de plateforme en haut de cet article.

Pour en savoir plus, consultez la [vue d’ensemble de l’évolutivité des performances][] (en anglais).

<a name="current-dwu-bk"></a>

## Afficher les paramètres d’unités DWU actuels

Pour afficher les paramètres d’unités DWU actuels pour vos bases de données :

1. Ouvrez l’Explorateur d’objets SQL Server dans Visual Studio 2015.
2. Connectez-vous à la base de données associée au serveur de base de données SQL logique.
2. Sélectionnez dans la vue de gestion dynamique sys.database\_service\_objectives. Voici un exemple : 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>

## Mettre à l'échelle les unités DWU

[AZURE.INCLUDE [Description de la mise à l’échelle des unités DWU SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les unités DWU :


1. Connectez-vous à la base de données associée à votre serveur de base de données SQL logique.
2. Utilisez l’instruction TSQL [ALTER DATABASE][]. L'exemple suivant définit l'objectif de niveau de service sur DW1000 pour la base de données MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## Étapes suivantes

Pour d'autres tâches de gestion, consultez la [vue d'ensemble de la gestion][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[vue d'ensemble de la gestion]: ./sql-data-warehouse-overview-manage.md
[vue d’ensemble de l’évolutivité des performances]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->