---
title: "Créer un pool de base de données élastique avec PowerShell | Microsoft Docs"
description: "Découvrez comment utiliser PowerShell pour augmenter la taille des instances de ressources de Base de données SQL Azure en créant un pool de base de données élastique évolutif afin de gérer plusieurs bases de données."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 37a707ee-9223-43ae-8c35-1ccafde8b83e
ms.service: sql-database
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: daf8bd6421ae563e542b0874a6e7748a3ca52738


---
# <a name="create-a-new-elastic-database-pool-with-powershell"></a>Créer un pool de base de données élastique avec PowerShell
> [!div class="op_single_selector"]
> * [Portail Azure](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C#](sql-database-elastic-pool-create-csharp.md)
> 
> 

Découvrez comment créer un [pool de base de données élastique](sql-database-elastic-pool.md) avec des applets de commande PowerShell. 

Pour connaître les codes d’erreur courants, consultez la page [Codes d’erreur SQL pour les applications clientes SQL Database : erreur de connexion à la base de données et autres problèmes](sql-database-develop-error-messages.md)

> [!NOTE]
> Les pools élastiques sont mis à la disposition générale dans toutes les régions Azure, à l’exception du Nord du centre des États-Unis et de l’Inde de l’Ouest, où ils sont actuellement en préversion.  Les pools élastiques seront mis à la disposition générale dans ces régions dès que possible. En outre, les pools élastiques ne prennent pas en charge les bases de données utilisant [l’OLTP ou l’analytique en mémoire](sql-database-in-memory.md).
> 
> 

Vous devez utiliser Azure PowerShell 1.0 ou une version supérieure. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

## <a name="create-a-new-pool"></a>Créer un pool
L’applet de commande [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378\(v=azure.300\).aspx) permet de créer un pool. Les valeurs correspondant au nombre d’eDTU par pool, ainsi qu’au nombre de DTU minimal et maximal, sont limitées par la valeur de niveau de service (de base, standard ou premium). Consultez l’article [Limites relatives aux eDTU et au stockage pour les pools et bases de données élastiques](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>Créer une base de données élastique dans un pool
Utilisez l’applet de commande [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) et définissez le paramètre **ElasticPoolName** sur le pool cible. Pour déplacer une base de données existante vers un pool, consultez l’article [Déplacer une base de données dans un pool élastique](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>Créer un pool et le remplir avec plusieurs nouvelles bases de données
La création d’un grand nombre de bases de données dans un pool peut prendre du temps si elle se fait par le biais du portail ou d’applets de commande PowerShell qui créent une seule base de données à la fois. Pour automatiser la création dans un nouveau pool, consultez l’article [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="example-create-a-pool-using-powershell"></a>Exemple : Création d’un pool à l’aide de PowerShell
Ce script crée un groupe de ressources Azure et un serveur. Lorsque vous y êtes invité, fournissez un nom d’utilisateur de l’administrateur et un mot de passe pour le nouveau serveur (différents de vos informations d’identification Azure).

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



## <a name="next-steps"></a>Étapes suivantes
* [Gérer votre pool](sql-database-elastic-pool-manage-powershell.md)
* [Créer des tâches élastiques](sql-database-elastic-jobs-overview.md) : les tâches élastiques vous permettent d’exécuter des scripts T-SQL, quel que soit le nombre de bases de données contenues dans le pool.
* [Montée en charge avec Base de données SQL Azure](sql-database-elastic-scale-introduction.md): utilisez les outils de base de données élastique pour monter en charge.




<!--HONumber=Nov16_HO2-->


