<properties 
    pageTitle="Créer un pool de base de données élastique (PowerShell) | Microsoft Azure" 
    description="Découvrez comment utiliser PowerShell pour mettre à l’échelle la base de données SQL Azure en créant un pool de base de données élastique pour gérer plusieurs bases de données." 
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="03/15/2016"
    ms.author="sstein"/>

# Créer un pool de base de données élastique (PowerShell) 

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Découvrez comment créer un [pool de base de données élastique](sql-database-elastic-pool.md) avec des applets de commande PowerShell.

Pour connaître les codes d’erreur courants, consultez la page [Codes d’erreur SQL pour les applications clientes SQL Database : erreur de connexion à la base de données et autres problèmes](sql-database-develop-error-messages.md)

> [AZURE.NOTE] Les pools élastiques de bases de données sont actuellement en version préliminaire et uniquement disponibles avec des serveurs SQL Database V12. Si vous disposez d’un serveur SQL Database V11, vous pouvez [utiliser PowerShell pour effectuer une mise à niveau vers V12 et créer un pool](sql-database-upgrade-server-portal.md) en une seule étape.


Vous devez utiliser Azure PowerShell 1.0 ou une version supérieure. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).



## Créer un pool de base de données élastique

L’applet de commande [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) permet de créer un pool de base de données élastique.

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## Créer une base de données élastique dans un pool de base de données élastique

Pour créer une base de données directement à l’intérieur d’un pool, utilisez l’applet de commande [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) et définissez le paramètre **ElasticPoolName**.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## Déplacer une base de données autonome dans un pool de bases de données élastique

Pour placer une base de données dans un pool, utilisez l’applet de commande [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) et définissez le paramètre **ElasticPoolName**.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## Créer un exemple de pool de base de données élastique (PowerShell)

Ce script permet de créer un serveur ; lorsqu’il vous demande un nom d’utilisateur et un mot de passe, entrez un nom d’utilisateur et un mot de passe d’administrateur pour votre nouveau serveur (pas vos informations d’identification Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## Étapes suivantes

- [Gérer votre pool](sql-database-elastic-pool-manage-powershell.md)
- [Créer des tâches élastiques](sql-database-elastic-jobs-overview.md) : les tâches élastiques facilitent l’exécution des scripts T-SQL, quel que soit le nombre de bases de données contenues dans le pool.


## Référence des bases de données élastiques

Pour en savoir plus sur les bases de données élastiques et les pools de bases de données élastiques, consultez l’article [Référence du pool de base de données élastique](sql-database-elastic-pool-reference.md).

<!---HONumber=AcomDC_0323_2016-->