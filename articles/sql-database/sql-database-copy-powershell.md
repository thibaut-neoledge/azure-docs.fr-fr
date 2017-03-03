---
title: "Copier une base de données SQL Azure à l’aide de PowerShell | Microsoft Docs"
description: "Création d&quot;une copie d&quot;une base de données SQL Azure à l&quot;aide de PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6d9839d7-9303-48d2-be0f-21ce84f95a94
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 09/08/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: edfbc0d94d9d5b33a25788772a98173187e394b8
ms.openlocfilehash: 031a17d179cee8d6cceb9ed3aefd993add1958b8
ms.lasthandoff: 02/11/2017


---
# <a name="copy-an-azure-sql-database-using-powershell"></a>Copier une base de données SQL Azure à l’aide de PowerShell

Cet article explique comment copier une base de données SQL avec PowerShell sur le même serveur ou sur un autre serveur et comment copier une base de données dans un [pool élastique](sql-database-elastic-pool.md). L’opération de copie de la base de données utilise l’applet de commande [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx) . 

> [!NOTE]
> Vous pouvez également copier une base de données SQL à l’aide du [portail Azure ](sql-database-copy-portal.md) ou de [Transact-SQL](sql-database-copy-transact-sql.md).
>

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* Une base de données SQL Azure (à copier). Si vous n’avez pas de base de données SQL, créez-en une en procédant de la manière décrite dans [Créer votre première base de données SQL Azure](sql-database-get-started.md).
* La version la plus récente d’Azure PowerShell. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

Nombre des nouvelles fonctionnalités de SQL Database ne sont prises en charge que si vous utilisez le [modèle de déploiement Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Les exemples cités utilisent donc les [applets de commande PowerShell Azure SQL Database](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx) pour Resource Manager. Les [applets de commande Azure SQL Database (Classic)](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) du modèle de déploiement Classic existant sont prises en charge à des fins de compatibilité descendante. Cependant, nous vous recommandons l’utilisation d’applets de commande Resource Manager.

> [!NOTE]
> Selon la taille de votre base de données, l’opération de copie peut prendre plus ou moins longtemps.
> 
> 

## <a name="copy-a-sql-database-to-the-same-server"></a>Copie d'une base de données SQL sur le même serveur
Pour créer la copie sur le même serveur, omettez le paramètre `-CopyServerName` (ou définissez-le sur le même serveur).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>Copie d'une base de données SQL vers un autre serveur
Pour créer la copie sur un autre serveur, incluez le paramètre `-CopyServerName` et définissez-le sur un autre serveur. Le serveur *copy* doit déjà exister. S’il se trouve dans un autre groupe de ressources, vous devez inclure également le paramètre `-CopyResourceGroupName` .

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-pool"></a>Copier une base de données SQL dans un pool élastique
Pour créer une copie d’une base de données SQL dans un pool, définissez le paramètre `-ElasticPoolName` sur un pool existant.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>Résolution des connexions
Pour résoudre les connexions à l’issue de l’opération de copie, consultez [Résoudre les connexions](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="example-powershell-script"></a>Exemple de script PowerShell
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

    # Copy a database into an elastic pool
    # ---------------------------------------------
    $poolName = "pool1"

    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName





## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données sur un autre serveur logique, consultez [Configurer et gérer la sécurité Azure SQL Database pour la géo-restauration ou le basculement](sql-database-geo-replication-security-config.md).
* Pour exporter une base de données dans un fichier BACPAC à l’aide de PowerShell, consultez [Export the database to a BACPAC using PowerShell](sql-database-export-powershell.md) (Exporter la base de données dans un fichier BACPAC à l’aide de PowerShell).
* [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
* [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

## <a name="additional-resources"></a>Ressources supplémentaires
* [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687\(v=azure.300\).aspx)
* [Gérer les connexions](sql-database-manage-logins.md)
* [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
* [Exporter la base de données vers un fichier BACPAC](sql-database-export.md)
* [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
* [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)
* [Référence d’applet de commande PowerShell Azure SQL Database](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx)


