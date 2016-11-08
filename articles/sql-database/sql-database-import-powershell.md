---
title: Importer un fichier BACPAC pour créer une base de données SQL Azure à l’aide de PowerShell | Microsoft Docs
description: Importer un fichier BACPAC pour créer une base de données SQL Azure à l’aide de PowerShell
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/31/2016
ms.author: sstein

---
# Importer un fichier BACPAC pour créer une base de données SQL Azure à l’aide de PowerShell
**Base de données unique**

> [!div class="op_single_selector"]
> * [Portail Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> 
> 

Cet article fournit des instructions pour créer une base de données SQL Azure en important un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) à l’aide de PowerShell.

La base de données est créée à partir d’un fichier BACPAC (.bacpac) importé depuis un conteneur d'objets blob de stockage Azure. Si vous n’avez pas de fichier BACPAC dans le stockage Azure, consultez [Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell](sql-database-export-powershell.md). Si vous disposez déjà d’un fichier BACPAC qui n’est pas dans le stockage Azure, [utilisez AzCopy pour le télécharger facilement vers votre compte de stockage Azure](../storage/storage-use-azcopy.md#blob-upload).

> [!NOTE]
> La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur que vous pouvez restaurer, et en assure la maintenance. Pour plus d’informations, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
> 
> 

Pour importer une base de données SQL, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous avez besoin d’un abonnement Azure, cliquez simplement sur **Version d’évaluation gratuite** en haut de cette page, puis continuez la lecture de cet article.
* Un fichier BACPAC de la base de données à importer. Le fichier BACPAC doit se trouver dans un conteneur d’objets blob de [compte de stockage Azure](../storage/storage-create-storage-account.md).

[!INCLUDE [Démarrer votre session PowerShell](../../includes/sql-database-powershell.md)]

## Configurer les variables de votre environnement
Il existe quelques variables pour lesquelles vous devez remplacer les valeurs d'exemple par les valeurs spécifiques de votre compte de stockage et de votre base de données.

Le nom du serveur doit être celui d’un serveur qui existe dans l’abonnement sélectionné à l’étape précédente. Il doit s’agir du serveur dans lequel vous voulez créer la base de données. L’importation directe d’une base de données dans un pool élastique n’est pas prise en charge. Mais vous pouvez d’abord l’importer dans une base de données unique, puis déplacer cette dernière vers le pool.

Le nom de la base de données est le nom que vous voulez donner à la nouvelle base de données.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


Les variables suivantes proviennent du compte de stockage dans lequel se trouve votre fichier BACPAC. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage pour obtenir ces valeurs. Vous pouvez trouver la clé primaire en cliquant sur **Tous les paramètres** puis sur **Clés** dans le panneau de votre compte de stockage.

Le nom de l'objet blob est le nom du fichier BACPAC existant à partir duquel vous souhaitez créer la base de données. Vous devez inclure l'extension .bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


L’exécution de l’applet de commande [Get-Credential](https://msdn.microsoft.com/library/hh849815.aspx) ouvre une fenêtre vous demandant votre nom d’utilisateur et votre mot de passe. Entrez le nom d’utilisateur et le mot de passe administrateur du serveur de base de données SQL ($ServerName ci-dessus), et non le nom d’utilisateur et le mot de passe de votre compte Azure.

    $credential = Get-Credential


## Importer la base de données
Cette commande envoie une demande d’importation de la base de données au service. Selon la taille de votre base de données, l'opération d'importation peut prendre plus ou moins longtemps.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## Surveillez la progression de l’opération
Après l’exécution de [New-AzureRmSqlDatabaseImport](https://msdn.microsoft.com/library/mt707793.aspx), vous pouvez vérifier l’état de la demande en exécutant [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## Script d'importation PowerShell de base de données SQL
    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## Étapes suivantes
* Pour vous connecter et interroger une base de données SQL importée, consultez [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)

<!---HONumber=AcomDC_0907_2016-->