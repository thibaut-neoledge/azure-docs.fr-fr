<properties 
    pageTitle="Créer et exporter un fichier BACPAC à partir d'une base de données SQL Azure à l’aide de PowerShell" 
    description="Créer et exporter un fichier BACPAC à partir d'une base de données SQL Azure à l’aide de PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/20/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Créer et exporter un fichier BACPAC à partir d'une base de données SQL Azure à l’aide de PowerShell

**Base de données unique**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Cet article fournit des instructions pour l’exportation d’un fichier BACPAC de votre base de données SQL Azure à l’aide de PowerShell.

Un BACPAC est un fichier .bacpac qui contient un schéma de base de données et des données. Pour plus d'informations, consultez Backup Package (.bacpac) dans [Applications de la couche Données](https://msdn.microsoft.com/library/ee210546.aspx).

> [AZURE.NOTE]La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur. Pour plus d’informations, consultez [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md).

Le fichier BACPAC est exporté dans un conteneur d'objets blob de stockage Azure que vous pouvez télécharger une fois l'opération terminée.


Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Une base de données SQL Azure. Si vous n’avez pas de base de données SQL, créez-en une en suivant les étapes figurant dans cet article : [Créer votre première base de données SQL Azure](sql-database-get-started.md).
- Un [compte Azure Storage](storage-create-storage-account.md) avec un conteneur d'objets blob pour stocker le fichier BACPAC. Actuellement, le compte de stockage doit utiliser le modèle de déploiement classique : choisissez par conséquent **Classique** lors de la création d'un compte de stockage.
- Azure PowerShell. Vous pouvez télécharger et installer les modules Azure PowerShell en exécutant [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](powershell-install-configure.md).



## Configurer vos informations d'identification et sélectionner votre abonnement

Tout d'abord, vous devez établir l'accès à votre compte Azure : lancez PowerShell, puis exécutez l’applet de commande suivante. Sur l’écran de connexion, saisissez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

	Add-AzureAccount

Après vous être connecté, vous voyez des informations sur l’écran, notamment l’ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.


### Sélectionner votre abonnement Azure

Pour sélectionner l’abonnement, vous avez besoin de votre identifiant ou de votre nom d’abonnement (**-SubscriptionName**). Vous pouvez copier l’identifiant d’abonnement à partir des informations affichées à l'étape précédente ou, si vous avez plusieurs abonnements et besoin de plus de détails, vous pouvez exécuter l'applet de commande **Get-AzureSubscription** et copier les informations d'abonnement souhaitées affichées dans les résultats. Une fois votre abonnement sélectionné, exécutez l'applet de commande suivante :

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Après avoir exécuté **Select-AzureSubscription**, vous êtes redirigé vers l’invite PowerShell. Si vous avez plusieurs abonnements, vous pouvez exécuter **Get-AzureSubscription** et vérifier que l’abonnement que vous voulez utiliser affiche **IsCurrent: True**.


## Configurer les variables en fonction de votre environnement

Il existe quelques variables pour lesquelles vous devez remplacer les valeurs d'exemple par les valeurs spécifiques de votre compte de stockage et de votre base de données.

Remplacez les noms de serveur et de base de données par le serveur et la base de données qui existent actuellement dans votre compte. Pour le nom de l'objet blob, entrez le nom du fichier BACPAC qui sera créé. Entrez le nom que vous souhaitez pour le fichier BACPAC, à condition qu’il comporte l'extension .bacpac.

    $ServerName = "servername"
    $DatabaseName = "nameofdatabasetobackup"
    $BlobName = "filename.bacpac"

Dans le [portail Azure en version préliminaire](https://portal.azure.com), accédez à votre compte de stockage pour obtenir ces valeurs. Vous pouvez trouver la clé primaire en cliquant sur **Tous les paramètres** puis sur **Clés** dans le panneau de votre compte de stockage.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"

## Créer un pointeur vers votre serveur et votre compte de stockage

L’exécution de l’applet de commande **Get-Credential** ouvre une fenêtre vous demandant votre nom d'utilisateur et votre mot de passe. Entrez le nom d’utilisateur et le mot de passe administrateur de votre serveur SQL (et NON le nom d'utilisateur et le mot de passe de votre compte Azure).

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Exporter votre base de données

Cette commande envoie une demande d’exportation de la base de données au service. Selon la taille de votre base de données, l'opération d'exportation peut prendre plus ou moins longtemps.

> [AZURE.IMPORTANT]Pour garantir un fichier BACPAC cohérent au niveau transactionnel, vous devez d’abord [créer une copie de votre base de données](sql-database-copy-powershell.md), puis exporter la copie de base de données.


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

<!---HONumber=Oct15_HO4-->