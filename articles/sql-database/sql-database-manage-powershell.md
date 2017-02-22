---
title: "Gérer la base de données SQL Azure avec PowerShell | Microsoft Docs"
description: "Gestion d’Azure SQL Database avec PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 3f21ad5e-ba99-4010-b244-5e5815074d31
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 32d8c5f13d95c3de8b06782f4f6541866389be5b


---
# <a name="managing-azure-sql-database-using-powershell"></a>Gestion d’Azure SQL Database à l’aide de PowerShell

Cette rubrique présente les applets de commande PowerShell utilisées pour effectuer de nombreuses tâches Azure SQL Database. Pour en obtenir la liste complète, consultez [Applets de commande d’Azure SQL Database](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx).

> [!TIP]
> Pour savoir comment créer un serveur ou un pare-feu basé sur un serveur, afficher les propriétés d’un serveur, se connecter à la base de données master et l’interroger, créer un exemple de base de données et une base de données vide, demander les propriétés d’une base de données, se connecter à l’exemple de base de données et l’interroger, consultez le didacticiel [Prise en main des serveurs Azure SQL Database, des bases de données et des règles de pare-feu à l’aide du portail Azure et de SQL Server Management Studio](sql-database-get-started-powershell.md).
>

## <a name="how-do-i-create-a-resource-group"></a>Comment créer un groupe de ressources ?
Pour créer un groupe de ressources pour notre base de données SQL Database et les ressources Azure associées, utilisez l’applet de commande [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837\(v=azure.300\).aspx).

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Pour plus d'informations, consultez [Utilisation d'Azure PowerShell avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).
Pour obtenir un didacticiel complet, voir [Prise en main des serveurs Azure SQL Database, des bases de données et des règles de pare-feu à l’aide d’Azure PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database-server"></a>Création d’un serveur de base de données SQL
Pour créer un serveur SQL Database, utilisez l’applet de commande [New-AzureRmSqlServer](/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver). Remplacez *server1* par le nom de votre serveur. Le nom doit être unique pour tous les serveurs Azure SQL Database. Vous obtiendrez une erreur si le nom est déjà utilisé pour un autre serveur. Cette commande peut prendre plusieurs minutes. Le groupe de ressources doit déjà exister dans votre abonnement.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Pour en savoir plus sur les serveurs, consultez la rubrique détaillant les [fonctionnalités de la base de données SQL](sql-database-features.md). Pour obtenir un didacticiel complet, voir [Prise en main des serveurs Azure SQL Database, des bases de données et des règles de pare-feu à l’aide d’Azure PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database-server-firewall-rule"></a>Créer une règle de pare-feu de serveur SQL Database
Pour créer une règle de pare-feu et accéder au serveur, utilisez l’applet de commande [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx). Exécutez la commande suivante en remplaçant les adresses IP de début et de fin par des valeurs valides pour votre client. Le groupe de ressources et le serveur doivent déjà exister dans votre abonnement.

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

Pour en savoir plus, consultez [Pare-feu de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Pour obtenir un didacticiel complet, voir [Prise en main des serveurs Azure SQL Database, des bases de données et des règles de pare-feu à l’aide d’Azure PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database"></a>Création d’une base de données SQL
Pour créer une base de données SQL, utilisez l’applet de commande [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx). Le groupe de ressources et le serveur doivent déjà exister dans votre abonnement. 

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

Pour plus d’informations, consultez [Qu’est-ce que SQL Database](sql-database-technical-overview.md). Pour obtenir un didacticiel complet, voir [Prise en main des serveurs Azure SQL Database, des bases de données et des règles de pare-feu à l’aide d’Azure PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-change-the-performance-level-of-a-sql-database"></a>Modifier le niveau de performances d’une base de données SQL
Pour modifier le niveau de performances, faites monter ou descendre en puissance votre base de données avec l’applet de commande [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx). Le groupe de ressources, le serveur et la base de données doivent déjà exister dans votre abonnement. Définissez `-RequestedServiceObjectiveName` sur un seul espace (comme dans l’extrait suivant) pour le niveau De base. Affectez-lui la valeur *S0*, *S1*, *P1*, *P6*, etc. comme dans l’exemple précédent pour les autres niveaux.

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

Pour plus d’informations, consultez [Options et performances de SQL Database : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md). Pour obtenir un exemple de script, consultez [Exemple de script PowerShell pour modifier les niveaux de service et de performances de votre base de données SQL](sql-database-manage-single-databases-powershell.md#change-the-service-tier-and-performance-level-of-a-single-database).

## <a name="how-do-i-copy-a-sql-database-to-the-same-server"></a>Copie d'une base de données SQL sur le même serveur
Pour copier une base de données SQL sur le même serveur, utilisez l’applet de commande [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx). Définissez `-CopyServerName` et `-CopyResourceGroupName` sur les mêmes valeurs que votre groupe de ressources et votre serveur de base de données source.

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

## <a name="how-do-i-delete-a-sql-database"></a>Comment supprimer une base de données SQL ?
Pour supprimer une base de données SQL, utilisez l’applet de commande [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368\(v=azure.300\).aspx). Le groupe de ressources, le serveur et la base de données doivent déjà exister dans votre abonnement.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-delete-a-sql-database-server"></a>Comment supprimer un serveur de base de données SQL ?
Pour supprimer un serveur SQL Database, utilisez l’applet de commande [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488\(v=azure.300\).aspx).

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-create-and-manage-elastic-pools-using-powershell"></a>Créer et gérer des pools élastiques à l’aide de PowerShell
Pour en savoir plus sur la création de pools élastiques avec PowerShell, voir [Créer un pool de bases de données élastiques avec PowerShell](sql-database-elastic-pool-manage-powershell.md).

Pour en savoir plus sur la gestion des pools élastiques avec PowerShell, voir [Surveiller et gérer un pool de bases de données élastiques avec PowerShell](sql-database-elastic-pool-manage-powershell.md).

## <a name="related-information"></a>Informations connexes
* [Applets de commande de la base de données SQL Azure.](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Guide de référence des applets de commande Azure](https://msdn.microsoft.com/library/azure/dn708514\(v=azure.300\).aspx)




<!--HONumber=Feb17_HO3-->


