<properties 
    pageTitle="Importer un fichier BACPAC vers une base de données SQL Azure à l’aide de PowerShell" 
    description="Importer un fichier BACPAC vers une base de données SQL Azure à l’aide de PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="09/23/2015"
    ms.author="sstein"/>

# Importer un fichier BACPAC vers une base de données SQL à l’aide de PowerShell

**Base de données unique**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)


Cet article vous montre comment créer une base de données SQL en important un fichier BACPAC avec PowerShell.

Un BACPAC est un fichier .bacpac qui contient un schéma de base de données et des données. Pour plus d'informations, consultez Backup Package (.bacpac) dans [Applications de la couche Données](https://msdn.microsoft.com/library/ee210546.aspx).

La base de données est créée à partir d'un fichier BACPAC importé depuis un conteneur d'objets blob de stockage Azure. Si vous n'avez pas de fichier .bacpac dans le stockage Azure, vous pouvez en créer un en suivant les étapes de [Créer et exporter un fichier BACPAC à partir d'une base de données SQL Azure](sql-database-backup.md).

> [AZURE.NOTE]La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur que vous pouvez restaurer, et en assure la maintenance. Pour plus d’informations, consultez [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md).


Pour importer une base de données SQL, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Un fichier .bacpac (BACPAC) de la base de données à restaurer. Le fichier BACPAC doit se trouver dans un conteneur d’objets blob de [Compte Azure Storage (classique)](storage-create-storage-account.md).
- Azure PowerShell. Vous pouvez télécharger et installer les modules Azure PowerShell en exécutant [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](powershell-install-configure.md).



## Configurer vos informations d'identification et sélectionner votre abonnement

Tout d'abord, vous devez établir l'accès à votre compte Azure : lancez PowerShell, puis exécutez l’applet de commande suivante. Sur l’écran de connexion, saisissez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

	Add-AzureAccount

Après vous être connecté, vous voyez des informations sur l’écran, notamment l’ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.


### Sélectionner votre abonnement Azure

Pour sélectionner l’abonnement, vous avez besoin de votre ID d’abonnement. Vous pouvez copier l’identifiant d’abonnement à partir des informations affichées à l'étape précédente ou, si vous avez plusieurs abonnements et besoin de plus de détails, vous pouvez exécuter l'applet de commande **Get-AzureSubscription** et copier les informations d'abonnement souhaitées affichées dans les résultats. Une fois en possession de votre identifiant d’abonnement, exécutez l'applet de commande suivante :

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Après avoir exécuté **Select-AzureSubscription**, vous êtes redirigé vers l’invite PowerShell. Si vous avez plusieurs abonnements, vous pouvez exécuter **Get-AzureSubscription** et vérifier que l’abonnement que vous avez sélectionné affiche **IsCurrent: True**.


## Configurer les variables de votre environnement

Il existe quelques variables pour lesquelles vous devez remplacer les valeurs d'exemple par les valeurs spécifiques de votre compte de stockage et de votre base de données.

Le nom du serveur doit être un serveur existant actuellement dans l'abonnement sélectionné à l'étape précédente. Il s’agit du serveur dans lequel vous souhaitez créer la base de données.

Le nom de la base de données est le nom que vous voulez donner à la nouvelle base de données.

    $ServerName = "servername"
    $DatabaseName = "databasename"


Les variables suivantes proviennent du compte de stockage dans lequel se trouve votre fichier BACPAC. Dans le [portail Azure en version préliminaire](https://portal.azure.com), accédez à votre compte de stockage pour obtenir ces valeurs. Vous pouvez trouver la clé primaire en cliquant sur **Tous les paramètres** puis sur **Clés** dans le panneau de votre compte de stockage.

Le nom de l'objet blob est le nom du fichier .bacpac existant à partir duquel vous souhaitez créer la base de données. Vous devez inclure l'extension .bacpac.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"

## Créer un pointeur vers votre serveur et votre compte de stockage

L’exécution de l’applet de commande **Get-Credential** ouvre une fenêtre vous demandant votre nom d'utilisateur et votre mot de passe. Entrez le nom d’utilisateur et le mot de passe administrateur du serveur SQL sur lequel vous souhaitez créer la base de données ($ServerName ci-dessus), et non le nom d'utilisateur et le mot de passe de votre compte Azure.

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Importer la base de données

Cette commande envoie une demande d’importation de la base de données au service. Selon la taille de votre base de données, l'opération d'importation peut prendre plus ou moins longtemps.

    $importRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Surveillez la progression de l’opération

Après l'exécution de **Start-AzureSqlDatabaseImport**, vous pouvez vérifier l'état de la demande.

En cas de vérification de l’état immédiatement après la demande, cela renvoie généralement un état **En attente** ou **En cours d'exécution** et vous donne le pourcentage de progression actuel. Vous pouvez donc exécuter cette opération plusieurs fois jusqu'à ce que vous voyiez **État : Terminé** en sortie.

Cette commande vous demandera un mot de passe. Entrez le nom d’utilisateur et le mot de passe administrateur de votre serveur SQL.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
 


## Script de restauration PowerShell de base de données SQL


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "nameofnewdatabase"

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $ImportRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    

## Étapes suivantes

- [Se connecter avec SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)




## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Exercices de récupération d'urgence](sql-database-disaster-recovery-drills.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Oct15_HO1-->