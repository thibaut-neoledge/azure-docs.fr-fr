<properties
	pageTitle="Gérer la base de données SQL Azure avec PowerShell | Microsoft Azure"
	description="Gestion d’Azure SQL Database avec PowerShell."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="sstein"/>

# Gérer la base de données SQL Azure avec PowerShell


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

Cette rubrique présente les applets de commande PowerShell utilisées pour effectuer de nombreuses tâches Azure SQL Database. Pour en obtenir la liste complète, consultez [Applets de commande d’Azure SQL Database](https://msdn.microsoft.com/library/mt574084.aspx).


## Créer un groupe de ressources

Créez un groupe de ressources pour notre base de données SQL Database et les ressources Azure associées avec l’applet de commande [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837.aspx).

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Pour plus d'informations, consultez [Utilisation d'Azure PowerShell avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md). Pour obtenir un exemple de script, consultez [Créer un script PowerShell de base de données SQL](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).

## Créer un serveur SQL Database

Créez un serveur SQL Database avec l’applet de commande [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Remplacez *server1* par le nom de votre serveur. Le nom doit être unique pour tous les serveurs Azure SQL Database. Vous obtiendrez une erreur si le nom est déjà utilisé pour un autre serveur. Cette commande peut prendre plusieurs minutes. Le groupe de ressources doit déjà exister dans votre abonnement.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Pour plus d’informations, consultez [Qu’est-ce que SQL Database](sql-database-technical-overview.md). Pour obtenir un exemple de script, consultez [Créer un script PowerShell de base de données SQL](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## Créer une règle de pare-feu de serveur SQL Database

Créez une règle de pare-feu et accéder au serveur avec l’applet de commande [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Exécutez la commande suivante en remplaçant les adresses IP de début et de fin par des valeurs valides pour votre client. Le groupe de ressources et le serveur doivent déjà exister dans votre abonnement.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Pour autoriser d’autres services Azure à accéder à votre serveur, créez une règle de pare-feu et définissez à la fois `-StartIpAddress` et `-EndIpAddress` sur **0.0.0.0**. Cette règle de pare-feu spéciale autorise tout le trafic Azure à accéder au serveur.

Pour en savoir plus, consultez [Pare-feu de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Pour obtenir un exemple de script, consultez [Créer un script PowerShell de base de données SQL](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## Créer une base de données SQL (vide)

Créez une base de données avec l’applet de commande [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). Le groupe de ressources et le serveur doivent déjà exister dans votre abonnement.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Pour plus d’informations, consultez [Qu’est-ce que SQL Database](sql-database-technical-overview.md). Pour obtenir un exemple de script, consultez [Créer un script PowerShell de base de données SQL](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## Modifier le niveau de performances d’une base de données SQL

Faites monter ou descendre en puissance votre base de données avec l’applet de commande [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). Le groupe de ressources, le serveur et la base de données doivent déjà exister dans votre abonnement. Définissez `-RequestedServiceObjectiveName` sur un seul espace (comme dans l’extrait suivant) pour le niveau De base. Affectez-lui la valeur *S0*, *S1*, *P1*, *P6*, etc. comme dans l’exemple précédent pour les autres niveaux.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Pour plus d’informations, consultez [Options et performances de SQL Database : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md). Pour obtenir un exemple de script, consultez [Exemple de script PowerShell pour modifier les niveaux de service et de performances de votre base de données SQL](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## Copie d'une base de données SQL sur le même serveur

Copiez une base de données SQL sur le même serveur avec l’applet de commande [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644.aspx). Définissez `-CopyServerName` et `-CopyResourceGroupName` sur les mêmes valeurs que votre groupe de ressources et votre serveur de base de données source.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Pour en savoir plus, consultez [Copie d’une base de données SQL Azure](sql-database-copy.md). Pour obtenir un exemple de script, consultez [Copier un script PowerShell de base de données SQL](sql-database-copy-powershell.md#example-powershell-script).


## Supprimer une base de données SQL

Supprimez une base de données SQL avec l’applet de commande [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx). Le groupe de ressources, le serveur et la base de données doivent déjà exister dans votre abonnement.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## Supprimer un serveur SQL Database

Supprimez un serveur avec l’applet de commande [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx).

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## Créer et gérer des pools de bases de données élastiques à l’aide de PowerShell

Pour plus d’informations sur la création de bases de données élastiques avec PowerShell, consultez [Créer un pool de base de données élastique avec PowerShell](sql-database-elastic-pool-create-powershell.md).

Pour plus d’informations sur la gestion de bases de données élastiques avec PowerShell, consultez [Surveiller et gérer un pool de base de données élastique avec PowerShell](sql-database-elastic-pool-manage-powershell.md).



## Informations connexes

- [Applets de commande de la base de données SQL Azure.](https://msdn.microsoft.com/library/azure/mt574084.aspx)
- [Guide de référence des applets de commande Azure](https://msdn.microsoft.com/library/azure/dn708514.aspx)

<!---HONumber=AcomDC_0914_2016-->