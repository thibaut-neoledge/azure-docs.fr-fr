<properties
   pageTitle="Gestion de la puissance de calcul dans Azure SQL Data Warehouse (REST) | Microsoft Azure"
   description="Tâches de l’API REST permettant de gérer la puissance de calcul. Mettez à l’échelle les ressources de calcul en ajustant les unités DWU. Ou suspendez et reprenez des ressources de calcul pour réduire les coûts."
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
   ms.date="05/14/2016"
   ms.author="barbkess;sonyama"/>

# Gestion de la puissance de calcul dans Azure SQL Data Warehouse (REST)

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-overview-manage-compute.md)
- [Portail](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Adaptez les performances en augmentant les ressources de calcul et la mémoire pour répondre à l’évolution des besoins de votre charge de travail. Réalisez des économies en réduisant vos ressources pendant les heures creuses ou en suspendant totalement vos ressources de calcul.

Cette collection de tâches utilise le portail Azure pour :

- Mise à l’échelle des ressources de calcul
- Suspension du calcul
- Reprise du calcul

Pour plus d’informations, consultez l’article [Manage compute power in Azure SQL Data Warehouse (Overview)][] (Gestion de la puissance de calcul dans Azure SQL Data Warehouse (Vue d’ensemble)).

<a name="scale-performance-bk"></a> <a name="scale-compute-bk"></a>

## Mise à l’échelle de la puissance de calcul

[AZURE.INCLUDE [Description de la mise à l’échelle des unités DWU SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les unités DWU, utilisez l’API REST [Créer ou mettre à jour une base de données][]. L'exemple suivant définit l'objectif de niveau de service sur DW1000 pour la base de données MySQLDW hébergée sur le serveur MyServer. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## Suspension du calcul

[AZURE.INCLUDE [Description de la suspension de SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Pour suspendre une base de données, utilisez l’API REST [Suspendre la base de données][]. Dans l’exemple suivant, une base de données appelée Database02 et hébergée sur un serveur appelé Server01 est interrompue. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## Reprise du calcul

[AZURE.INCLUDE [Description de la reprise de SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]

Pour démarrer une base de données, utilisez l’API REST [Reprendre la base données][]. Dans l’exemple suivant, une base de données appelée Database02 et hébergée sur un serveur appelé Server01 est démarrée. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## Étapes suivantes

Pour d'autres tâches de gestion, consultez la rubrique [Vue d'ensemble de la gestion][].

<!--Image references-->

<!--Article references-->
[Vue d'ensemble de la gestion]: ./sql-data-warehouse-overview-manage.md
[Performance scalability overview]: ./sql-data-warehouse-overview-scalability.md
[Manage compute power in Azure SQL Data Warehouse (Overview)]: ./sql-data-warehouse-overview-manage-compute.md

<!--MSDN references-->
[Suspendre la base de données]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[Reprendre la base données]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Créer ou mettre à jour une base de données]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0518_2016-->