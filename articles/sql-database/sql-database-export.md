---
title: "Exporter une base de données SQL Azure dans un fichier BACPAC | Microsoft Docs"
description: "Exporter une base de données SQL Azure dans un fichier BACPAC à l’aide du Portail Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: move data
ms.devlang: NA
ms.date: 04/05/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 06183c5e1b61c47a7229c2abcc64217ee57c2bac
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exporter une base de données SQL Azure dans un fichier BACPAC

Cet article décrit l’exportation d’une base de données SQL Azure dans un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Cet article aborde l’utilisation des méthodes suivantes :
- [Portail Azure](https://portal.azure.com)
- Utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)
- Applet de commande [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport)
- Assistant [Exporter une application de la couche Données](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) dans [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).

> [!IMPORTANT] 
> La fonctionnalité Automatiser l’exportation Azure SQL Database est maintenant disponible en version préliminaire et sera supprimée le 1er mars 2017. À partir du 1er décembre 2016, vous ne pourrez plus configurer l’exportation automatisée sur une base de données SQL. Tous vos travaux d’exportation automatisée existants continueront à fonctionner jusqu’au 1er mars 2017. Après le 1er décembre 2016, vous pouvez utiliser [Rétention des sauvegardes à long terme](sql-database-long-term-retention.md
) ou [Azure Automation](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) pour archiver des bases de données SQL régulièrement à l’aide de PowerShell en fonction d’une planification de votre choix. Pour obtenir un exemple de script, vous pouvez télécharger [l’exemple de script PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) sur GitHub.
>

> [!IMPORTANT] 
> La fonctionnalité Automatiser l’exportation Azure SQL Database est maintenant disponible en version préliminaire et sera supprimée le 1er mars 2017. À partir du 1er décembre 2016, vous ne pourrez plus configurer l’exportation automatisée sur une base de données SQL. Tous vos travaux d’exportation automatisée existants continueront à fonctionner jusqu’au 1er mars 2017. Après le 1er décembre 2016, vous pouvez utiliser [Rétention des sauvegardes à long terme](sql-database-long-term-retention.md
) ou [Azure Automation](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) pour archiver des bases de données SQL régulièrement à l’aide de PowerShell en fonction d’une planification de votre choix. Pour obtenir un exemple de script, vous pouvez télécharger [l’exemple de script PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) sur GitHub.
>

## <a name="overview"></a>Vue d'ensemble

Lorsque vous avez besoin d’exporter une base de données à des fins d’archivage ou de migration vers une autre plate-forme, vous pouvez exporter le schéma de base de données et les données dans un fichier BACPAC. Un fichier BACPAC est un fichier ZIP avec une extension de fichier BACPAC contenant les métadonnées et les données à partir d’une base de données SQL Server. Il peut être stocké dans le Stockage Blob Azure ou un stockage local dans un emplacement local, puis importé dans Azure SQL Database ou une installation locale SQL Server. 

> [!IMPORTANT]
> Si vous exportez depuis SQL Server avant de procéder à la migration vers Azure SQL Database, consultez [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md).
> 

## <a name="considerations"></a>Considérations

* Pour qu’un export soit cohérent au niveau transactionnel, vous devez vérifier qu’aucune activité d’écriture n’a lieu lors de l’exportation ou effectuer une exportation à partir d’une [copie cohérente au niveau transactionnel](sql-database-copy.md) de votre base de données SQL Azure.
* Si vous exportez dans le stockage Blob, la taille maximale d’un fichier BACPAC est de 200 Go. Pour archiver un fichier BACPAC plus volumineux, exportez dans le stockage local.
* L’exportation d’un fichier BACPAC vers le stockage Premium Azure en utilisant les méthodes décrites dans cet article n’est pas prise en charge.
* Si l’opération d’exportation depuis Azure SQL Database dure plus de 20 heures, elle peut être annulée. Pour améliorer les performances lors de l’exportation, vous pouvez :
  * Augmentez temporairement votre niveau de service.
  * Interrompez toutes les activités de lecture et d’écriture lors de l’exportation.
  * Utilisez un [index cluster](https://msdn.microsoft.com/library/ms190457.aspx) avec des valeurs non nulles sur toutes les tables de grande taille. Sans index cluster, une exportation peut échouer si elle dure plus de 6 à 12 heures. Cela est dû au fait que les services d’exportation doivent effectuer une analyse complète de la table pour tenter de l’exporter en entier. Une bonne méthode pour déterminer si vos tables sont optimisées pour l’exportation consiste à exécuter **DBCC SHOW_STATISTICS** et à vérifier que *RANGE_HI_KEY* n’est pas nul et que sa valeur a une bonne distribution. Pour plus d’informations, consultez [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> Les BACPAC ne sont pas conçus pour être utilisés pour les opérations de sauvegarde et de restauration. La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur. Pour plus d’informations, consultez [Vue d’ensemble de la continuité de l’activité avec la base de données SQL Azure](sql-database-business-continuity.md) et [Découvrir les sauvegardes SQL Database](sql-database-automated-backups.md).  
> 

## <a name="azure-portal"></a>Portail Azure

Pour exporter une base de données à l’aide du portail Azure, ouvrez la page de votre base de données, puis cliquez sur **Exporter** sur la barre d’outils. Spécifiez le nom de fichier *.bacpac, fournissez le compte de stockage Azure et le conteneur pour l’exportation, et fournissez les informations d’identification pour la connexion à la base de données source.  

   ![Exportation de base de données](./media/sql-database-export/database-export.png)

Pour surveiller la progression de l’opération d’exportation, ouvrez la page du serveur logique qui contient la base de données en cours d’exportation. Faites défiler jusqu'à **Operations**, puis cliquez sur l’historique **Import/Export**.

## <a name="sqlpackage-utility"></a>Utilitaire SQLPackage

Pour exporter une base de données SQL à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx), consultez [Paramètres et propriétés d’exportation](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). L’utilitaire SQLPackage est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et de [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez également télécharger la dernière version de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) directement à partir du Centre de téléchargement Microsoft.

Nous recommandons l’utilisation de l’utilitaire SQLPackage afin de bénéficier de l’évolutivité et des performances dans la plupart des environnements de production. Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Cet exemple montre comment exporter une base de données à l’aide de SqlPackage.exe avec l’authentification Active Directory universelle :

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio"></a>SQL Server Management Studio

Les versions les plus récentes de SQL Server Management Studio fournissent également un Assistant pour exporter une base de données SQL Azure dans un fichier bacpac. Consultez [Exporter une application de la couche Données](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="powershell"></a>PowerShell

Utilisez l’applet de commande [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) pour soumettre une demande d’exportation de base de données au service de base de données SQL Azure. Selon la taille de votre base de données, l'opération d'exportation peut prendre plus ou moins longtemps.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Pour vérifier l’état de la demande d’exportation, utilisez l’applet de commande [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus). L’exécution immédiatement après la demande retourne généralement **État : en cours**. Lorsque **État : réussite** s’affiche, l’exportation est terminée.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

### <a name="export-sql-database-example"></a>Exemple d’exportation de base de données SQL
L’exemple suivant exporte une base de données SQL Azure existante dans un fichier BACPAC puis montre comment vérifier l’état de l’opération d’exportation.

Pour exécuter l’exemple, il existe quelques variables pour lesquelles vous devez remplacer les valeurs par les valeurs spécifiques de votre compte de stockage et de votre base de données. Dans le [Portail Azure](https://portal.azure.com), accédez à votre compte de stockage pour obtenir le nom du compte de stockage, le nom du conteneur d’objets blob et la valeur de clé. Vous pouvez trouver la clé en cliquant sur **Clés d’accès** dans le panneau de votre compte de stockage.

Remplacez les valeurs `VARIABLE-VALUES` suivantes par les valeurs de vos ressources Azure spécifiques. Le nom de la base de données est celui de la base de données existante que vous souhaitez exporter.

```powershell
$subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId

# Database to export
$DatabaseName = "DATABASE-NAME"
$ResourceGroupName = "RESOURCE-GROUP-NAME"
$ServerName = "SERVER-NAME
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
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la rétention des sauvegardes à long terme d’une sauvegarde de base de données SQL Azure comme alternative à l’exportation d’une base de données à des fins d’archivage, consultez [Rétention à long terme](sql-database-long-term-retention.md).
- Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Pour en savoir plus sur l’importation d’un fichier BACPAC dans une base de données SQL Server, consultez [Importer un fichier BACPCAC dans une base de données SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
* Pour en savoir plus sur l’exportation d’un fichier BACPAC à partir d’une base de données SQL Server, consultez [Exporter une application de la couche Données](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) et [Migrer votre première base de données](sql-database-migrate-your-sql-server-database.md).

