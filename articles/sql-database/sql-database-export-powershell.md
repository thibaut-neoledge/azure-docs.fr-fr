---
title: "Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell"
description: "Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 08/15/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: c886e8e61b00de2f07d7f5a98c2d2f4d5b29b7cf
ms.openlocfilehash: dd264dfc73962a575f0d4b1a32a9ec02752c33ba


---
# <a name="archive-an-azure-sql-database-to-a-bacpac-file-by-using-powershell"></a>Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell
> [!div class="op_single_selector"]
> * [Portail Azure](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

Cet article fournit des instructions pour archiver votre base de données SQL Azure à partir d’un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) (stocké dans Azure Blob Storage) à l’aide de PowerShell.

Lorsque vous avez besoin d’archiver une base de données SQL Azure, vous pouvez exporter le schéma et les données associés dans un fichier BACPAC. Un fichier BACPAC est un fichier ZIP avec l’extension .bacpac. Un fichier BACPAC peut être stocké ultérieurement dans le stockage d’objets Blob Azure ou dans le stockage d’un emplacement local. Il peut également être réimporté dans la base de données SQL Azure ou dans une installation SQL Server locale.

## <a name="considerations"></a>Considérations

* Pour qu’une archive soit cohérente au niveau transactionnel, vous devez vérifier qu’aucune activité d’écriture n’a lieu lors de l’exportation ou effectuer une exportation à partir d’une [copie cohérente au niveau transactionnel](sql-database-copy.md) de votre base de données SQL Azure.
* La taille maximale d’un fichier BACPAC archivé dans Azure Blob Storage est de 200 Go. Pour archiver un fichier BACPAC plus volumineux dans un stockage local, utilisez l’utilitaire d’invite de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Cet utilitaire est fourni avec Visual Studio et SQL Server. Vous pouvez également [télécharger](https://msdn.microsoft.com/library/mt204009.aspx) la dernière version de SQL Server Data Tools pour obtenir cet utilitaire.
* L’archivage dans Azure Premium Storage à l’aide d’un fichier BACPAC n’est pas pris en charge.
* Si l’opération d’exportation dure plus de 20 heures, elle peut être annulée. Pour améliorer les performances lors de l’exportation, vous pouvez :
  * Augmentez temporairement votre niveau de service.
  * Interrompez toutes les activités de lecture et d’écriture lors de l’exportation.
  * Utilisez un [index cluster](https://msdn.microsoft.com/library/ms190457.aspx) avec des valeurs non nulles sur toutes les tables de grande taille. Sans index cluster, une exportation peut échouer si elle dure plus de 6 à 12 heures. Cela est dû au fait que les services d’exportation doivent effectuer une analyse complète de la table pour tenter de l’exporter en entier. Une bonne méthode pour déterminer si vos tables sont optimisées pour l’exportation consiste à exécuter **DBCC SHOW_STATISTICS** et à vérifier que *RANGE_HI_KEY* n’est pas nul et que sa valeur a une bonne distribution. Pour plus d’informations, consultez [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> Les BACPAC ne sont pas conçus pour être utilisés pour les opérations de sauvegarde et de restauration. La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur. Pour plus d’informations, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
> 
> 

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* Un abonnement Azure.
* base de données SQL Azure.
* Un [compte Azure Storage standard](../storage/storage-create-storage-account.md), avec un conteneur d’objets blob pour stocker le fichier BACPAC dans le stockage standard.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="export-your-database"></a>Exporter votre base de données
L’applet de commande [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx) soumet une demande d’exportation de base de données au service. Selon la taille de votre base de données, l'opération d'exportation peut prendre plus ou moins longtemps.

> [!IMPORTANT]
> Pour garantir un fichier BACPAC cohérent au niveau transactionnel, vous devez d’abord [créer une copie de votre base de données](sql-database-copy-powershell.md), puis exporter la copie de base de données.
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>Surveillez la progression de l’opération d’exportation
Après l’exécution de [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx), vous pouvez vérifier l’état de la demande en exécutant [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx). L’exécution immédiatement après la demande retourne généralement **État : en cours**. Lorsque vous voyez **État : réussite** , l’exportation est terminée.

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
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink

## <a name="automate-export-using-azure-automation"></a>Automatiser l’exportation à l’aide d’Azure Automation

Vous pouvez utiliser Azure Automation pour archiver des bases de données SQL à intervalles réguliers, suivant la planification de votre choix. Vous pouvez télécharger [l’exemple de script sur GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). Pour plus d’informations sur Azure Automation, consultez [Vue d’ensemble d’Azure Automation](../automation/automation-intro.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur l’importation d’une base de données SQL Azure à l’aide de PowerShell, consultez [Importer un fichier BACPAC à l’aide de PowerShell](sql-database-import-powershell.md).

## <a name="additional-resources"></a>Ressources supplémentaires
* [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx)




<!--HONumber=Nov16_HO3-->


