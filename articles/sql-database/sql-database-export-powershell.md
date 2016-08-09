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
	ms.date="07/19/2016"
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
 
> [AZURE.NOTE] Les BACPAC ne sont pas conçus pour être utilisés pour les opérations de sauvegarde et de restauration. La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur. Pour plus d’informations, consultez : [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure.
- base de données SQL Azure.
- Un [compte Azure Storage standard](../storage/storage-create-storage-account.md) avec un conteneur d’objets blob pour stocker le fichier BACPAC dans le stockage standard.


[AZURE.INCLUDE [Démarrer votre session PowerShell](../../includes/sql-database-powershell.md)]


## Configurer les variables en fonction de votre environnement

Il existe quelques variables pour lesquelles vous devez remplacer les valeurs d'exemple par les valeurs spécifiques de votre compte de stockage et de votre base de données.

Effectuez un remplacement par vos propres valeurs.

    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"


Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage pour obtenir ces valeurs. Vous pouvez trouver la clé primaire en cliquant sur **Tous les paramètres** puis sur **Clés** dans le panneau de votre compte de stockage.

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


L’exécution de l’applet de commande **Get-Credential** ouvre une fenêtre vous demandant votre nom d’utilisateur et votre mot de passe. Entrez le nom d’utilisateur et le mot de passe administrateur de votre serveur SQL (et NON le nom d'utilisateur et le mot de passe de votre compte Azure).

    $credential = Get-Credential

## Exporter votre base de données

Cette commande envoie une demande d’exportation de la base de données au service. Selon la taille de votre base de données, l'opération d'exportation peut prendre plus ou moins longtemps.

> [AZURE.IMPORTANT] Pour garantir un fichier BACPAC cohérent au niveau transactionnel, vous devez d’abord [créer une copie de votre base de données](sql-database-copy-powershell.md), puis exporter la copie de base de données.


    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password
    

## Surveillez la progression de l’opération d’exportation

Après l’exécution de **New-AzureRmSqlDatabaseExport**, vous pouvez vérifier l’état de la demande. En cas d’exécution immédiatement après la demande, cela renvoie généralement **État : En attente** ou **État : En cours d’exécution**. Vous pouvez donc exécuter cette opération plusieurs fois jusqu’à ce que vous voyiez **État : Terminé** en sortie.

Cette commande vous demandera un mot de passe. Entrez le mot de passe administrateur de votre serveur SQL.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink
    


## Exporter un script PowerShell de base de données SQL


    $ServerName = "servername"
    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName  –AdministratorPassword $credential.Password

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink


## Étapes suivantes

- Pour en savoir plus sur l’importation d’une base de données Azure SQL à l’aide de Powershell, consultez [Importer un fichier BACPAC à l’aide de PowerShell](sql-database-import-powershell.md)

<!---HONumber=AcomDC_0727_2016-->