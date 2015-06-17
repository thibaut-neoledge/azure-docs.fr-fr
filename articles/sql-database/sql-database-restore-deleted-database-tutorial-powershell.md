<properties 
   pageTitle="Restauration d'une base de données SQL Azure supprimée dans Azure PowerShell" 
   description="base de données SQL Microsoft Azure, restaurer une base de données supprimée, récupérer une base de données supprimée, Azure PowerShell" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Restauration d'une base de données SQL Azure supprimée dans Azure PowerShell

> [AZURE.SELECTOR]
- [Restauration d'une base de données supprimée - Portail](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)
- [Restauration d'une base de données supprimée - API REST](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-rest/)

## Présentation

Ce didacticiel vous montre comment restaurer une base de données SQL Azure supprimée dans [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Vous pouvez restaurer une base de données qui avait été supprimée pendant sa période de rétention jusqu'à la date et l'heure de sa suppression. La période de rétention est déterminée par le niveau de service de la base de données.

La restauration d'une base de données SQL Azure supprimée entraîne la création d'une nouvelle base de données. Le service sélectionne automatiquement le niveau de service en fonction de la sauvegarde utilisée au point de restauration. Assurez-vous que le quota disponible sur le serveur logique est suffisant pour permettre la création d'une autre base de données. Si vous avez besoin d'un quota supérieur, contactez le [support technique d'Azure](http://azure.microsoft.com/support/options/).

## Restrictions et sécurité

Consultez [Restauration d'une base de données SQL Azure supprimée dans le portail Azure](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/).

## Procédure : restauration d'une base de données SQL Azure supprimée dans Azure PowerShell

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-Deleted-SQL-Database-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Vous devez utiliser l'authentification par certificat pour exécuter les applets de commande suivantes. Pour plus d'informations, consultez la section  *Utilisation de la méthode par certificat* dans l'article [Comment installer et configurer Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method).

1. Obtenez la liste des bases de données récupérables en utilisant l'applet de commande [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx).
	* Utilisez le commutateur **RestorableDropped** et spécifiez le paramètre **ServerName** désignant le serveur où a été supprimée la base de données.
	* Exécutez la commande suivante pour stocker les résultats dans une variable appelée **$RecoverableDBs**.
	
	`PS C:>$RecoverableDBs = Get-AzureSqlDatabase -ServerName "myserver" -RestorableDropped`

2. Dans la liste des bases de données supprimées, choisissez la base de données supprimée que vous voulez restaurer.

	* Tapez le numéro de la base de données supprimée qui figure dans la liste **$RecoverableDBs**.  

	`PS C:>$Database = $RecoverableDBs[<deleted database number>]`

	* Pour plus d'informations sur l'obtention d'une base de données supprimée pouvant être restaurée, consultez [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/dn546735.aspx).

3. Lancez la restauration avec l'applet de commande [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx). Spécifiez les paramètres suivants :	
	* **SourceRestorableDroppedDatabase**
	* **TargetDatabaseName**, qui désigne la base de données dans laquelle vous allez effectuer la restauration.

	Stockez les données retournées dans une variable appelée **$RestoreRequest**. Cette variable contient l'ID de demande de restauration qui est utilisé pour surveiller l'état de la restauration.
	
	`PS C:>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database -TargetDatabaseName "myrestoredDB"`

Le processus de restauration peut prendre du temps. Pour surveiller l'état de la restauration, utilisez l'applet de commande [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) et spécifiez les paramètres suivants :

* **ServerName**, qui désigne le serveur où se trouve la base de données dans laquelle vous effectuez la restauration.
* **OperationGuid**, qui est l'ID de demande de restauration ayant été stocké dans la variable **$RestoreRequest** à l'étape 3.

	`PS C:>Get-AzureSqlDatabaseOperation -ServerName "myserver" -OperationGuid $RestoreRequest.RequestID`

Les champs **State** et **PercentComplete** indiquent l'état de la restauration.

## Étapes suivantes

Pour plus d'informations, consultez les liens suivants :

[Continuité des activités de l'entreprise dans la base de données SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sauvegarde et restauration de base de données SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx)
<!--HONumber=47-->
 