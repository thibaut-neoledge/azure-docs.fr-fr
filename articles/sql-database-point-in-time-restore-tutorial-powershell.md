<properties 
   pageTitle="Restauration d'une base de données SQL Azure à l'aide d'une restauration jusqu'à une date et heure dans Azure PowerShell" 
   description="restauration jusqu'à une date et heure, base de données SQL Microsoft Azure, restaurer une base de données, récupérer une base de données, Azure PowerShell" 
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

# Restauration d'une base de données SQL Azure à l'aide d'une restauration jusqu'à une date et heure dans Azure PowerShell

> [AZURE.SELECTOR]
- [Restauration jusqu'à une date et heure - Portail](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)
- [Restauration jusqu'à une date et heure - API REST](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-rest/) 

## Présentation

Ce didacticiel vous montre comment restaurer une base de données SQL Azure à l'aide de la restauration jusqu'à une date et heure dans [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Une base de données SQL Azure présente des sauvegardes intégrées pour prendre en charge la restauration en libre-service jusqu'à une date et heure pour les niveaux de service De base, Standard et Premium.

La restauration jusqu'à une date et heure entraîne la création d'une nouvelle base de données. Le service sélectionne automatiquement le niveau de service en fonction de la sauvegarde utilisée au point de restauration. Assurez-vous que le quota disponible sur le serveur logique est suffisant pour permettre la création d'une autre base de données. Si vous avez besoin d'un quota supérieur, contactez le [support technique d'Azure](http://azure.microsoft.com/support/options/).

## Restrictions et sécurité

Consultez [Restauration d'une base de données SQL Azure à l'aide d'une restauration jusqu'à une date et heure dans le portail Azure](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/).

## Procédure : restauration d'une base de données SQL Azure à l'aide d'une restauration jusqu'à une date et heure dans Azure PowerShell

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Point-in-Time-Restore-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Vous devez utiliser l'authentification par certificat pour exécuter les applets de commande suivantes. Pour plus d'informations, consultez la section  *Use the certificate method* dans l'article [Comment installer et configurer Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method).

1. Obtenez la base de données que vous souhaitez restaurer à l'aide de l'applet de commande [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx). Spécifiez les paramètres suivants :
	* **ServerName**, qui désigne le serveur où se trouve la base de données.
	* **DatabaseName**, qui désigne la base de données à restaurer.	

	`PS C:>$Database = Get-AzureSqlDatabase -ServerName "myserver" -DatabaseName "mydb"`

2. Lancez la restauration avec l'applet de commande [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx). Spécifiez les paramètres suivants :	
	* **SourceDatabase**, qui désigne la base de données à restaurer.
	* **TargetDatabaseName**, qui désigne la base de données dans laquelle vous allez effectuer la restauration.
	* **PointInTime**, qui indique la date et l'heure jusqu'où vous voulez effectuer la restauration.

	Stockez les données retournées dans une variable appelée **$RestoreRequest**. Cette variable contient l'ID de demande de restauration qui est utilisé pour surveiller l'état de la restauration. 

	`PS C:>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database -TargetDatabaseName "myrestoredDB" -PointInTime "2015-01-01 06:00:00"`

Le processus de restauration peut prendre du temps. Pour surveiller l'état de la restauration, utilisez l'applet de commande [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) et spécifiez les paramètres suivants :

* **ServerName**, qui désigne le serveur où se trouve la base de données dans laquelle vous effectuez la restauration.
* **OperationGuid**, qui est l'ID de demande de restauration ayant été stocké dans la variable **$RestoreRequest** à l'étape 2.

	`PS C:>Get-AzureSqlDatabaseOperation -ServerName "myserver" -OperationGuid $RestoreRequest.RequestID`

Les champs **State** et **PercentComplete** indiquent l'état de la restauration. 

## Étapes suivantes

Pour plus d'informations, consultez les liens suivants :  

[Continuité des activités de l'entreprise dans la base de données SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sauvegarde et restauration de base de données SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Restauration jusqu'à une date et heure d'une base de données SQL Azure (blog)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

<!--HONumber=47-->
