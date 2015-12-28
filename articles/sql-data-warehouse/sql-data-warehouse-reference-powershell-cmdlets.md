<properties
   pageTitle="Prise en main avec les applets de commande dans SQL Data Warehouse"
   description="Interrompre et redémarrer SQL Data Warehouse à l’aide d’applets de commande PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sidneyh"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/15/2015"
   ms.author="twounder;sidneyh;barbkess"/>

# Prise en main avec les applets de commande Azure Data Warehouse et les API REST

SQL Data Warehouse peut être géré à l’aide d’applets de commande Azure PowerShell ou d’API REST.

Les commandes définies pour la **base de données Azure SQL** sont également utilisées pour **SQL Data Warehouse**. Pour consulter une liste actualisée, accédez à la section [Applets de commande Azure SQL](https://msdn.microsoft.com/library/mt574084.aspx). Les applets de commande **Suspend-AzureSqlDatabase** et **Resume-AzureSqlDatabase** (ci-dessous) sont des ajouts spécialement conçus pour SQL Data Warehouse.

De la même manière, les API REST dédiés à la **base de données SQL Azure** peuvent également être utilisés avec les instances **SQL Data Warehouse**. Pour consulter une liste actualisée, accédez à la page [Opérations pour les bases de données SQL Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx).

## Acquérir et exécuter les applets de commande Azure PowerShell

1. Pour télécharger le module Azure PowerShell, exécutez [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). 
2. Pour exécuter le module, tapez **Microsoft Azure PowerShell** dans la fenêtre de démarrage.
3. Si vous n’avez pas encore ajouté votre compte à la machine, exécutez l’applet de commande suivante. (Pour plus d’informations, consultez la rubrique [Comment installer et configurer Azure PowerShell]() :

		Add-AzureAccount
3. Changez de mode avec cette applet de commande :

		Switch-AzureMode AzureResourceManager

## Suspend-AzureSqlDatabase
### Exemple 1 : Interruption d’une base de données par nom sur un serveur

Dans cet exemple, une base de données appelée « Database02 » et hébergée sur un serveur appelé « Server01 » est interrompue. Le serveur est un groupe de ressources Azure appelé « ResourceGroup1 ».

    Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
    "Server01" –DatabaseName "Database02"

### Exemple 2 : Interruption d’un objet de base de données

Dans cet exemple, une base de données appelée « Database02 » est récupérée d’un serveur appelé « Server01 » hébergé dans un groupe de ressources appelé « ResourceGroup1 ». L’objet récupéré est redirigé vers **Suspend-AzureSqlDatabase**. En conséquence, la base de données est interrompue.

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$resultDatabase = $database | Suspend-AzureSqlDatabase

## Resume-AzureSqlDatabase

### Exemple 1 : Reprise d’une base de données par nom sur un serveur

Dans cet exemple, les opérations d’une base de données appelée « Database02 » et hébergée sur un serveur « Server01 » sont reprises. Le serveur est hébergé dans un groupe de ressources appelé « ResourceGroup1 ».

	Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"

### Exemple 2 : Reprise d’un objet de base de données

Dans cet exemple, une base de données appelée « Database02 » est récupérée d’un serveur appelé « Server01 » hébergé dans un groupe de ressources appelé « ResourceGroup1 ». L’objet est redirigé vers **Resume-AzureSqlDatabase**.

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$resultDatabase = $database | Resume-AzureSqlDatabase

## Get-AzureSqlDatabaseRestorePoints

Cette applet de commande répertorie les points de restauration de sauvegarde associés à une base de données SQL. Les points de restauration sont utilisés pour restaurer la base de données. Les propriétés de l’objet renvoyé sont les suivantes.

Propriété|Description
---|---
RestorePointType|DISCRETS / CONTINUS. Les points de restauration discrets sont les éventuels points dans le temps auxquels une base de données Azure SQL peut être restaurée. Les points de restauration continus sont les points dans le temps les plus antérieurs auxquels une base de données Azure SQL peut être restaurée. La base de données peut être restaurée à tout point dans le temps postérieur au point le plus antérieur.
EarliestRestoreDate|Point de restauration le plus antérieur (rempli quand restorePointType = CONTINU)
RestorePointCreationDate |Prise de l’instantané de sauvegarde (rempli quand restorePointType = DISCRET)

### Exemple 1: Extraction des points de restauration de base de données par nom sur un serveur
Dans cet exemple, les points de restauration d’une base de données appelée « Database02 » sont récupérés d’un serveur appelé « Server01 » hébergé dans un groupe de ressources appelé « ResourceGroup1 ».

	$restorePoints = Get-AzureSqlDatabaseRestorePoints –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"



### Exemple 2 : Reprise d’un objet de base de données

Dans cet exemple, une base de données appelée « Database02 » est récupérée d’un serveur appelé « Server01 » hébergé dans un groupe de ressources appelé « ResourceGroup1 ». L’objet de base de données est dirigé vers **Get-AzureSqlDatabase**, et le résultat comprend les points de restauration de la base de données.

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$restorePoints = $database | Get-AzureSqlDatabaseRestorePoints



> [AZURE.NOTE]Notez que si votre serveur est nommé foo.database.windows.net, utilisez « foo » en tant que nom du serveur dans les applets de commande powershell.


## Étapes suivantes
Pour plus d’informations, consultez la [vue d’ensemble de référence de SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble de référence de SQL Data Warehouse]: sql-data-warehouse-overview-reference.md
[How to install and configure Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->


<!--Other Web references-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=AcomDC_1217_2015-->