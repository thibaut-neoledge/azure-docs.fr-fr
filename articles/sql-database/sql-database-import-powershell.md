---
title: "Importer un fichier BACPAC pour créer une base de données SQL Azure à l’aide de PowerShell | Microsoft Docs"
description: "Importer un fichier BACPAC pour créer une base de données SQL Azure à l’aide de PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 8d78da13-43fe-4447-92e0-0a41d0321fd4
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 02/07/2017
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 45ec817e62e7967549602adfd2c9d2d3f2484987
ms.lasthandoff: 02/11/2017


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importer un fichier BACPAC pour créer une base de données SQL Azure à l’aide de PowerShell

Cet article fournit des instructions pour créer une base de données SQL Azure en important un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) à l’aide de PowerShell.

## <a name="prequisites"></a>Configuration requise

Pour importer une base de données SQL, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous avez besoin d’un abonnement Azure, cliquez simplement sur **Version d’évaluation gratuite** en haut de cette page, puis continuez la lecture de cet article.
* Un fichier BACPAC de la base de données à importer. Le fichier BACPAC doit se trouver dans un conteneur d’objets blob de [compte de stockage Azure](../storage/storage-create-storage-account.md) .

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="set-up-the-variables-for-your-environment"></a>Configurer les variables de votre environnement
Il existe quelques variables pour lesquelles vous devez remplacer les valeurs d'exemple par les valeurs spécifiques de votre compte de stockage et de votre base de données.

Le nom du serveur doit être celui d’un serveur qui existe dans l’abonnement sélectionné à l’étape précédente. Il doit s’agir du serveur dans lequel vous voulez créer la base de données. L’importation directe d’une base de données dans un pool élastique n’est pas prise en charge. Mais vous pouvez d’abord l’importer sous forme de base de données unique, puis déplacer cette dernière vers le pool.

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


L’exécution de l’applet de commande [Get-Credential](https://msdn.microsoft.com/library/azure/hh849815\(v=azure.300\).aspx) ouvre une fenêtre vous demandant votre nom d’utilisateur et votre mot de passe. Entrez le nom d’utilisateur et le mot de passe administrateur du serveur de base de données SQL ($ServerName ci-dessus), et non le nom d’utilisateur et le mot de passe de votre compte Azure.

    $credential = Get-Credential


## <a name="import-the-database"></a>Importer la base de données
Cette commande envoie une demande d’importation de la base de données au service. Selon la taille de votre base de données, l'opération d'importation peut prendre plus ou moins longtemps.

    $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -StorageKeytype $StorageKeyType -StorageKey $StorageKey -StorageUri $StorageUri -AdministratorLogin $credential.UserName -AdministratorLoginPassword $credential.Password -Edition Standard -ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>Surveillez la progression de l’opération
Après l’exécution de [New-AzureRmSqlDatabaseImport](https://msdn.microsoft.com/library/azure/mt707793\(v=azure.300\).aspx), vous pouvez vérifier l’état de la demande en exécutant [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>Script d'importation PowerShell de base de données SQL
    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -StorageKeytype $StorageKeyType -StorageKey $StorageKey -StorageUri $StorageUri -AdministratorLogin $credential.UserName -AdministratorLoginPassword $credential.Password -Edition Standard -ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>Étapes suivantes
* Pour vous connecter et interroger une base de données SQL importée, consultez [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md).
* Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Pour une description du processus complet de migration d’une base de données SQL Server, notamment les recommandations relatives aux performances, consultez [Migrer une base de données SQL Server vers Azure SQL Database](sql-database-cloud-migrate.md).



