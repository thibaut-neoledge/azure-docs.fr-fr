<properties 
   pageTitle="Récupération d’une base de données SQL Azure à l’aide de la géo-restauration utilisant l’API REST" 
   description="géo-restauration, base de données SQL Microsoft Azure, restaurer une base de données, récupérer une base de données, API REST" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="03/17/2015"
   ms.author="elfish; v-romcal"/>

# Récupération d’une base de données SQL Azure à l’aide de la géo-restauration utilisant l’API REST

> [AZURE.SELECTOR]
- [Geo-Restore - portal](sql-database-geo-restore-tutorial-management-portal.md)
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)

## Vue d’ensemble

Ce didacticiel vous montre comment récupérer une base de données SQL Azure en utilisant l’API REST. Il fournit également des liens vers des procédures plus détaillées. La géo-restauration est la protection de récupération d’urgence de base qui est fournie pour tous les niveaux de service De base, Standard et Premium des bases de données SQL Azure.

## Restrictions et sécurité

Consultez [Récupérer une base de données SQL Azure à l’aide de la géo-restauration dans le portail Azure](sql-database-geo-restore-tutorial-management-portal.md).

## Comment : récupération d’une base de données SQL Azure avec l’API REST

1.	Obtenez la liste des bases de données récupérables à l’aide de l’opération [List Recoverable Databases](http://msdn.microsoft.com/library/azure/dn800984.aspx).
	
2.	Obtenez la base de données à récupérer à l'aide de l'opération [Get Recoverable Database](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Créez la requête de récupération à l'aide de l'opération [Create Database Recovery Request](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Suivez l’état de la récupération à l’aide de l’opération [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Étapes suivantes

Pour plus d’informations, consultez les liens suivants :

[Restaurer une base de données SQL Azure à l’aide d’une restauration jusqu’à une date et heure dans le portail Azure](sql-database-point-in-time-restore-tutorial-management-portal.md)

[Restaurer une base de données SQL Azure supprimée dans le portail Azure](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Continuité de l’activité des bases de données SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sauvegarde et restauration de base de données SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Géo-restauration de base de données SQL Azure (blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Service Management REST API Reference](https://msdn.microsoft.com/library/azure/ee460799.aspx)
 

<!---HONumber=62-->