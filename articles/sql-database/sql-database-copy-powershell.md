<properties 
    pageTitle="Copier une base de données SQL Azure à l’aide de PowerShell | Microsoft Azure" 
    description="Création d'une copie d'une base de données SQL Azure à l'aide de PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/08/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Copier une base de données SQL Azure à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-copy.md)
- [Portail Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Cet article explique comment copier une base de données SQL avec PowerShell sur le même serveur ou sur un autre serveur et comment copier une base de données dans un [pool de base de données élastique](sql-database-elastic-pool.md). L’opération de copie de la base de données utilise l’applet de commande [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644.aspx).


Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- Une base de données SQL Azure (à copier). Si vous n’avez pas de base de données SQL, créez-en une en procédant de la manière décrite dans [Créer votre première base de données SQL Azure](sql-database-get-started.md).
- La version la plus récente d’Azure PowerShell. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).


Nombre des nouvelles fonctionnalités de SQL Database ne sont prises en charge que si vous utilisez le [modèle de déploiement Azure Resource Manager](../resource-group-overview.md). Les exemples cités utilisent donc les [applets de commande PowerShell Azure SQL Database](https://msdn.microsoft.com/library/azure/mt574084.aspx) pour Resource Manager. Les [applets de commande Azure SQL Database (Classic)](https://msdn.microsoft.com/library/azure/dn546723.aspx) du modèle de déploiement Classic existant sont prises en charge à des fins de compatibilité descendante. Cependant, nous vous recommandons l’utilisation d’applets de commande Resource Manager.


>[AZURE.NOTE] Selon la taille de votre base de données, l’opération de copie peut prendre plus ou moins longtemps.


## Copie d'une base de données SQL sur le même serveur

Pour créer la copie sur le même serveur, omettez le paramètre `-CopyServerName` (ou définissez-le sur le même serveur).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## Copie d'une base de données SQL vers un autre serveur

Pour créer la copie sur un autre serveur, incluez le paramètre `-CopyServerName` et définissez-le sur un autre serveur. Le serveur *copy* doit déjà exister. S’il se trouve dans un autre groupe de ressources, vous devez inclure également le paramètre `-CopyResourceGroupName`.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## Copier une base de données SQL dans un pool de base de données élastique

Pour créer une copie d’une base de données SQL dans un pool, définissez le paramètre `-ElasticPoolName` sur un pool existant.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## Résolution des connexions

Pour résoudre les connexions à l’issue de l’opération de copie, consultez [Résoudre les connexions](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## Exemple de script PowerShell

Le script suivant part du principe que tous les groupes de ressources, les serveurs et le pool existent déjà (remplacez les valeurs des variables par vos ressources existantes). Tout doit exister, à l’exception de la copie de la base de données.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId
    
    
    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"
    
    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"
    
    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName
    
    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName
    
    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"
    
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName



    

## Étapes suivantes

- Consultez la page [Copie d’une base de données SQL Azure](sql-database-copy.md) pour une vue d’ensemble de la copie d’une base de données SQL Azure.
- Consultez [Copier une base de données SQL Azure à l’aide du Portail Azure](sql-database-copy-portal.md) pour copier une base de données à l’aide du Portail Azure.
- Consultez la page [Copier une base de données SQL Azure à l’aide de T-SQL](sql-database-copy-transact-sql.md) pour copier une base de données à l’aide de Transact-SQL.
- Consultez la page [Gestion de la sécurité d’une base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données vers un autre serveur logique.


## Ressources supplémentaires

- [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
- [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
- [Gérer les connexions](sql-database-manage-logins.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données vers un fichier BACPAC](sql-database-export.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Référence d’applet de commande PowerShell Azure SQL Database](https://msdn.microsoft.com/library/mt574084.aspx)

<!---HONumber=AcomDC_0914_2016-->