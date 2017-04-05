---
title: "PowerShell : Exporter une base de données SQL Azure dans un fichier BACPAC | Microsoft Docs"
description: "Exporter une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 41774c7f1c038ee017d719e59ed79fb68999dac8
ms.lasthandoff: 03/29/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-to-a-bacpac-file-by-using-powershell"></a>Exporter une base de données SQL Azure ou SQL Server dans un fichier BACPAC à l’aide de PowerShell

Cet article fournit des instructions pour exporter votre base de données SQL Azure ou une base de données SQL Server dans un fichier BACPAC (stocké dans le stockage Blob Azure) à l’aide de PowerShell. Pour une vue d’ensemble de l’exportation dans un fichier BACPAC, consultez [Export to a BACPAC](sql-database-export.md) (Exporter dans un fichier BACPAC).

> [!NOTE]
> Vous pouvez également exporter votre fichier de base de données SQL Azure dans un fichier BACPAC à l’aide du [portail Azure](sql-database-export-portal.md), de [SQL Server Management Studio](sql-database-export-ssms.md) ou de [SQLPackage](sql-database-export-sqlpackage.md).
>

## <a name="prerequisites"></a>Composants requis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* Un abonnement Azure.
* base de données SQL Azure.
* Un [compte Azure Storage standard](../storage/storage-create-storage-account.md), avec un conteneur d’objets blob pour stocker le fichier BACPAC dans le stockage standard.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="export-your-database"></a>Exporter votre base de données
L’applet de commande [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) soumet une demande d’exportation de base de données au service. Selon la taille de votre base de données, l'opération d'exportation peut prendre plus ou moins longtemps.

> [!IMPORTANT]
> Pour garantir un fichier BACPAC cohérent au niveau transactionnel, vous devez d’abord [créer une copie de votre base de données](scripts/sql-database-copy-database-to-new-server-powershell.md), puis exporter la copie de base de données.
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>Surveillez la progression de l’opération d’exportation
Après l’exécution de [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com//powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport), vous pouvez vérifier l’état de la demande en exécutant [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus). L’exécution immédiatement après la demande retourne généralement **État : en cours**. Lorsque **État : réussite** s’affiche, l’exportation est terminée.

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>Exemple d’exportation de base de données SQL
L’exemple suivant exporte une base de données SQL existante vers un fichier BACPAC puis montre comment vérifier l’état de l’opération d’exportation.

Pour exécuter l’exemple, il existe quelques variables pour lesquelles vous devez remplacer les valeurs par les valeurs spécifiques de votre compte de stockage et de votre base de données. Dans le [Portail Azure](https://portal.azure.com), accédez à votre compte de stockage pour obtenir le nom du compte de stockage, le nom du conteneur d’objets blob et la valeur de clé. Vous pouvez trouver la clé en cliquant sur **Clés d’accès** dans le panneau de votre compte de stockage.

Remplacez les valeurs `VARIABLE-VALUES` suivantes par les valeurs de vos ressources Azure spécifiques. Le nom de la base de données est celui de la base de données existante que vous souhaitez exporter.

    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
    $creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink

## <a name="automate-export-using-azure-automation"></a>Automatiser l’exportation à l’aide d’Azure Automation

La fonctionnalité Automatiser l’exportation Azure SQL Database est maintenant disponible en version préliminaire et sera supprimée le 1er mars 2017. À partir du 1er décembre 2016, vous ne pourrez plus configurer l’exportation automatisée sur une base de données SQL. Tous vos travaux d’exportation automatisée existants continueront à fonctionner jusqu’au 1er mars 2017. Après le 1er décembre 2016, vous pouvez utiliser [Rétention des sauvegardes à long terme](sql-database-long-term-retention.md) ou [Azure Automation](../automation/automation-intro.md) pour archiver des bases de données SQL régulièrement à l’aide de PowerShell en fonction d’une planification de votre choix. Pour obtenir un exemple de script, vous pouvez télécharger [l’exemple de script sur GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 


## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur l’importation d’une base de données SQL Azure à l’aide de PowerShell, consultez [Importer un fichier BACPAC à l’aide de PowerShell](scripts/sql-database-import-from-bacpac-powershell.md).
* Pour en savoir plus sur l’importation d’un fichier BACPAC à l’aide de SQLPackage, consultez [Import a BACPAC to Azure SQL Database using SqlPackage](sql-database-import-sqlpackage.md) (Importer un fichier BACPAC dans Azure SQL Database à l’aide de SqlPackage)
* Pour en savoir plus sur l’importation d’un fichier BACPAC à l’aide du portail Azure, consultez [Import a BACPAC to Azure SQL Database using the Azure portal](sql-database-import-portal.md) (Importer un fichier BACPAC dans Azure SQL Database à l’aide du portail Azure)
* Pour une description du processus complet de migration d’une base de données SQL Server, y compris les recommandations relatives aux performances, consultez [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md).
* Pour en savoir plus sur la rétention des sauvegardes à long terme d’une sauvegarde de base de données SQL Azure comme alternative à l’exportation d’une base de données à des fins d’archivage, consultez [Rétention à long terme](sql-database-long-term-retention.md)
* Pour en savoir plus sur l’importation d’un fichier BACPAC dans une base de données SQL Server, consultez [Importer un fichier BACPCAC dans une base de données SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)



## <a name="additional-resources"></a>Ressources supplémentaires
* [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus)


