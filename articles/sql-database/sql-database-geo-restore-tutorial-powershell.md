<properties 
   pageTitle="Récupération d'une base de données SQL Azure à l'aide de la géo-restauration dans Azure PowerShell" 
   description="géo-restauration, base de données SQL Microsoft Azure, restaurer une base de données, récupérer une base de données, Azure PowerShell" 
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

# Récupération d'une base de données SQL Azure à l'aide de la géo-restauration dans Azure PowerShell

> [AZURE.SELECTOR]
- [Géo-restauration - Portail](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/)
- [Géo-restauration - API REST](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-rest/)

## Présentation

Ce didacticiel vous montre comment récupérer une base de données SQL Azure à l'aide de la géo-restauration dans [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). La géo-restauration est la protection de récupération d'urgence de base qui est fournie pour tous les niveaux de service De base, Standard et Premium des bases de données SQL Azure.

## Restrictions et sécurité

Consultez [Récupération d'une base de données SQL Azure à l'aide de la géo-restauration dans le portail Azure](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/).

## Procédure : récupération d'une base de données SQL Azure à l'aide de la géo-restauration dans Azure PowerShell

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Geo-Restore-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Vous devez utiliser l'authentification par certificat pour exécuter les applets de commande suivantes. Pour plus d'informations, consultez la section  *Utilisation de la méthode par certificat* dans l'article [Comment installer et configurer Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method).

1. Obtenez la liste des bases de données récupérables en utilisant l'applet de commande [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx). Spécifiez le paramètre suivant :
	* **ServerName**, qui désigne le serveur où se trouve la base de données.	

	`PS C:>Get-AzureSqlRecoverableDatabase -ServerName "myserver"`

2. Choisissez la base de données que vous souhaitez récupérer à l'aide de l'applet de commande [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx). Spécifiez les paramètres suivants :
	* **ServerName**, qui désigne le serveur où se trouve la base de données.
	* **DatabaseName**, qui désigne la base de données à récupérer.

	`PS C:>$Database = Get-AzureSqlRecoverableDatabase -ServerName "myserver" -DatabaseName "mydb"`
	 
3. Lancez la récupération avec l'applet de commande [Start-AzureSqlDatabaseRecovery](http://msdn.microsoft.com/library/dn720224.aspx). Spécifiez les paramètres suivants :	
	* **SourceDatabase**, qui désigne la base de données à récupérer.
	* **TargetDatabaseName**, qui désigne la base de données dans laquelle vous allez effectuer la récupération.
	* **TargetServerName**, qui désigne le serveur sur lequel vous souhaitez récupérer la base de données.

	Stockez les données retournées dans une variable appelée **$RestoreRequest**. Cette variable contient l'ID de demande de restauration qui est utilisé pour surveiller l'état de la restauration.

	`PS C:>$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database -TargetDatabaseName "myrecoveredDB" -TargetServerName "mytargetserver"`
	
Le processus de récupération d'une base de données peut prendre du temps. Pour surveiller l'état de la récupération, utilisez l'applet de commande [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) et spécifiez les paramètres suivants :

* **ServerName**, qui désigne le serveur où se trouve la base de données dans laquelle vous effectuez la restauration.
* **OperationGuid**, qui est l'ID de demande de restauration ayant été stocké dans la variable **$RecoveryRequest** à l'étape 3.

	`PS C:>Get-AzureSqlDatabaseOperation -ServerName "mytargetserver" -OperationGuid $RecoveryRequest.ID`

Les champs **State** et **PercentComplete** indiquent l'état de la restauration.

## Étapes suivantes

Pour plus d'informations, consultez les liens suivants :  

[Restauration d'une base de données SQL Azure à l'aide d'une restauration jusqu'à une date et heure dans le portail Azure](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)

[Restauration d'une base de données SQL Azure supprimée dans le portail Azure](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)

[Continuité des activités de l'entreprise dans la base de données SQL Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sauvegarde et restauration de base de données SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Géo-restauration de base de données SQL Azure (blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

<!--HONumber=47-->
 