<properties 
    pageTitle="Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell" 
    description="Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Cet article fournit des instructions pour archiver votre base de données SQL Azure à partir d’un fichier BACPAC stocké dans Azure Blob Storage à l’aide de PowerShell.

Lorsque vous avez besoin d’archiver une base de données SQL Azure, vous pouvez exporter le schéma et les données associés dans un fichier BACPAC. Un fichier BACPAC est un fichier ZIP avec l’extension BACPAC. Il peut être stocké ultérieurement dans Azure Blob Storage ou un stockage local dans un emplacement local, puis importé dans Azure SQL Database ou une installation locale SQL Server.

***Considérations***

- Pour qu’une archive soit cohérente au niveau transactionnel, vous devez vérifier qu’aucune activité d’écriture n’a lieu lors de l’exportation ou effectuer une exportation à partir d’une [copie cohérente au niveau transactionnel](sql-database-copy.md) de votre base de données SQL Azure.
- La taille maximale d’un fichier BACPAC archivé dans Azure Blob Storage est de 200 Go. L’utilitaire d’invite de commandes [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) permet d’archiver un fichier BACPAC plus volumineux dans un stockage local. Cet utilitaire est fourni avec Visual Studio et SQL Server. Vous pouvez également [télécharger](https://msdn.microsoft.com/library/mt204009.aspx) la dernière version de SQL Server Data Tools pour obtenir cet utilitaire.
- L’archivage dans Azure Premium Storage à l’aide d’un fichier BACPAC n’est pas pris en charge.
- Si l’opération d’exportation dure plus de 20 heures, elle peut être annulée. Pour améliorer les performances lors de l’exportation, vous pouvez :
 - Augmenter temporairement votre niveau de service 
 - Interrompre toutes les activités de lecture et d’écriture lors de l’exportation
 - Utiliser un index cluster sur toutes les tables de grande taille. Sans index cluster, une exportation peut échouer si elle dure plus de 6 à 12 heures. Cela est dû au fait que les services d’exportation doivent effectuer une analyse complète de la table pour tenter de l’exporter en entier.
 
> [AZURE.NOTE] Les BACPAC ne sont pas conçus pour être utilisés pour les opérations de sauvegarde et de restauration. La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur. Pour plus d’informations, consultez [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md).

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. 
- Une base de données SQL Azure. 
- Un [compte Azure Storage standard](../storage/storage-create-storage-account.md) avec un conteneur d’objets blob pour stocker le fichier BACPAC dans le stockage standard.
- Azure PowerShell. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).


## Configurer vos informations d'identification et sélectionner votre abonnement

Tout d'abord, vous devez établir l'accès à votre compte Azure : lancez PowerShell, puis exécutez l’applet de commande suivante. Sur l’écran de connexion, saisissez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

	Add-AzureAccount

Après vous être connecté, vous voyez des informations sur l’écran, notamment l’ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.


### Sélectionner votre abonnement Azure

Pour sélectionner l'abonnement, vous avez besoin de votre identifiant ou de votre nom d'abonnement (**-SubscriptionName**). Vous pouvez copier l'identifiant d'abonnement à partir des informations affichées à l'étape précédente ou, si vous avez plusieurs abonnements et besoin de plus de détails, vous pouvez exécuter l'applet de commande **Get-AzureSubscription** et copier les informations d'abonnement souhaitées affichées dans les résultats. Une fois votre abonnement sélectionné, exécutez l'applet de commande suivante :

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Après avoir exécuté **Select-AzureSubscription**, vous êtes redirigé vers l’invite PowerShell. Si vous avez plusieurs abonnements, vous pouvez exécuter **Get-AzureSubscription** et vérifier que l’abonnement que vous voulez utiliser affiche **IsCurrent: True**.


## Configurer les variables en fonction de votre environnement

Il existe quelques variables pour lesquelles vous devez remplacer les valeurs d'exemple par les valeurs spécifiques de votre compte de stockage et de votre base de données.

Remplacez les noms de serveur et de base de données par le serveur et la base de données qui existent actuellement dans votre compte. Pour le nom de l'objet blob, entrez le nom du fichier BACPAC qui sera créé. Entrez le nom que vous souhaitez pour le fichier BACPAC, à condition qu’il comporte l'extension .bacpac.

    $ServerName = "servername"
    $DatabaseName = "nameofdatabasetoexport"
    $BlobName = "filename.bacpac"

Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage pour obtenir ces valeurs. Vous pouvez trouver la clé primaire en cliquant sur **Tous les paramètres** puis sur **Clés** dans le panneau de votre compte de stockage.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"

## Créer un pointeur vers votre serveur et votre compte de stockage

L’exécution de l’applet de commande **Get-Credential** ouvre une fenêtre vous demandant votre nom d’utilisateur et votre mot de passe. Entrez le nom d’utilisateur et le mot de passe administrateur de votre serveur SQL (et NON le nom d'utilisateur et le mot de passe de votre compte Azure).

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Exporter votre base de données

Cette commande envoie une demande d’exportation de la base de données au service. Selon la taille de votre base de données, l'opération d'exportation peut prendre plus ou moins longtemps.

> [AZURE.IMPORTANT] Pour garantir un fichier BACPAC cohérent au niveau transactionnel, vous devez d’abord [créer une copie de votre base de données](sql-database-copy-powershell.md), puis exporter la copie de base de données.


    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Surveillez la progression de l’opération d’exportation

Après l’exécution de **Start-AzureSqlDatabaseExport**, vous pouvez vérifier l’état de la demande. En cas d’exécution immédiatement après la demande, cela renvoie généralement **État : En attente** ou **État : En cours d’exécution**. Vous pouvez donc exécuter cette opération plusieurs fois jusqu’à ce que vous voyiez **État : Terminé** en sortie.

Cette commande vous demandera un mot de passe. Entrez le mot de passe administrateur de votre serveur SQL.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## Exporter un script PowerShell de base de données SQL


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "databasename"
    $BlobName = "bacpacfilename"
    
    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## Étapes suivantes

- [Importation d'une base de données SQL Azure](sql-database-import-powershell.md)


## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Exercices de récupération d'urgence](sql-database-disaster-recovery-drills.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0525_2016-->